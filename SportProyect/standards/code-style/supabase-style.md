# Supabase Style Guide para SportProyect

## Configuración y Setup

### Cliente Supabase

```typescript
// supabase.config.ts
import { createClient } from '@supabase/supabase-js';
import { Database } from './database.types';

const supabaseUrl = process.env.SUPABASE_URL!;
const supabaseAnonKey = process.env.SUPABASE_ANON_KEY!;

export const supabase = createClient<Database>(supabaseUrl, supabaseAnonKey, {
  auth: {
    autoRefreshToken: true,
    persistSession: true,
    detectSessionInUrl: true,
    storage: typeof window !== 'undefined' ? window.localStorage : undefined,
  },
  global: {
    headers: {
      'x-application-name': 'SportProyect'
    }
  },
  db: {
    schema: 'public'
  }
});

// For server-side usage (with service role key)
export const supabaseAdmin = createClient<Database>(
  supabaseUrl,
  process.env.SUPABASE_SERVICE_ROLE_KEY!,
  {
    auth: {
      autoRefreshToken: false,
      persistSession: false
    }
  }
);
```

## Database Types Generation

```bash
# Generate TypeScript types from your Supabase database
npx supabase gen types typescript --project-id your-project-id > src/types/database.types.ts
```

### Generated Types Example

```typescript
export type Database = {
  public: {
    Tables: {
      profiles: {
        Row: {
          id: string;
          email: string;
          username: string;
          first_name: string;
          last_name: string;
          avatar_url: string | null;
          role: 'admin' | 'coach' | 'player' | 'viewer';
          created_at: string;
          updated_at: string;
        };
        Insert: {
          id: string;
          email: string;
          username: string;
          first_name: string;
          last_name: string;
          avatar_url?: string | null;
          role?: 'admin' | 'coach' | 'player' | 'viewer';
          created_at?: string;
          updated_at?: string;
        };
        Update: {
          email?: string;
          username?: string;
          first_name?: string;
          last_name?: string;
          avatar_url?: string | null;
          role?: 'admin' | 'coach' | 'player' | 'viewer';
          updated_at?: string;
        };
      };
      teams: {
        Row: {
          id: string;
          name: string;
          city: string;
          founded: number;
          stadium: string | null;
          logo_url: string | null;
          created_at: string;
          created_by: string;
        };
        Insert: {
          id?: string;
          name: string;
          city: string;
          founded: number;
          stadium?: string | null;
          logo_url?: string | null;
          created_at?: string;
          created_by: string;
        };
        Update: {
          name?: string;
          city?: string;
          founded?: number;
          stadium?: string | null;
          logo_url?: string | null;
        };
      };
    };
    Views: {
      team_statistics: {
        Row: {
          team_id: string;
          team_name: string;
          total_matches: number;
          wins: number;
          draws: number;
          losses: number;
          goals_for: number;
          goals_against: number;
        };
      };
    };
    Functions: {
      get_user_teams: {
        Args: { user_id: string };
        Returns: {
          id: string;
          name: string;
          role: string;
        }[];
      };
    };
  };
};
```

## Authentication Patterns

### Sign Up with Profile Creation

```typescript
export async function signUpWithProfile(
  email: string,
  password: string,
  userData: {
    username: string;
    firstName: string;
    lastName: string;
  }
) {
  // Start a Supabase transaction
  const { data: authData, error: authError } = await supabase.auth.signUp({
    email,
    password,
    options: {
      data: {
        username: userData.username,
        first_name: userData.firstName,
        last_name: userData.lastName,
      },
      emailRedirectTo: `${window.location.origin}/auth/callback`,
    },
  });

  if (authError) throw authError;

  // Profile is created automatically via database trigger
  return authData;
}
```

### Session Management

```typescript
export class AuthManager {
  private static instance: AuthManager;
  private sessionCheckInterval: NodeJS.Timeout | null = null;

  private constructor() {
    this.initializeSessionCheck();
  }

  static getInstance(): AuthManager {
    if (!AuthManager.instance) {
      AuthManager.instance = new AuthManager();
    }
    return AuthManager.instance;
  }

  private initializeSessionCheck() {
    // Check session every 5 minutes
    this.sessionCheckInterval = setInterval(async () => {
      const { data: { session }, error } = await supabase.auth.getSession();
      
      if (error || !session) {
        // Handle session expiry
        await this.handleSessionExpiry();
      }
    }, 5 * 60 * 1000);

    // Listen for auth state changes
    supabase.auth.onAuthStateChange(async (event, session) => {
      switch (event) {
        case 'SIGNED_IN':
          await this.handleSignIn(session);
          break;
        case 'SIGNED_OUT':
          await this.handleSignOut();
          break;
        case 'TOKEN_REFRESHED':
          console.log('Token refreshed successfully');
          break;
        case 'USER_UPDATED':
          await this.handleUserUpdate(session);
          break;
      }
    });
  }

  async getCurrentUser() {
    const { data: { user }, error } = await supabase.auth.getUser();
    if (error) throw error;
    return user;
  }

  async refreshSession() {
    const { data: { session }, error } = await supabase.auth.refreshSession();
    if (error) throw error;
    return session;
  }

  private async handleSessionExpiry() {
    // Clear local data
    localStorage.removeItem('app-state');
    // Redirect to login
    window.location.href = '/auth/login?reason=session-expired';
  }

  private async handleSignIn(session: Session | null) {
    if (!session?.user) return;

    // Load user profile
    const { data: profile } = await supabase
      .from('profiles')
      .select('*')
      .eq('id', session.user.id)
      .single();

    // Store in app state
    if (profile) {
      localStorage.setItem('user-profile', JSON.stringify(profile));
    }
  }

  private async handleSignOut() {
    // Clear all local data
    localStorage.clear();
    sessionStorage.clear();
  }

  private async handleUserUpdate(session: Session | null) {
    if (!session?.user) return;
    
    // Refresh user profile
    await this.handleSignIn(session);
  }

  destroy() {
    if (this.sessionCheckInterval) {
      clearInterval(this.sessionCheckInterval);
    }
  }
}
```

## Database Queries

### Type-Safe Query Builder

```typescript
// Base repository pattern
export abstract class BaseRepository<T extends { id: string }> {
  constructor(
    protected tableName: string,
    protected supabase: SupabaseClient<Database>
  ) {}

  async findById(id: string): Promise<T | null> {
    const { data, error } = await this.supabase
      .from(this.tableName)
      .select('*')
      .eq('id', id)
      .single();

    if (error) {
      if (error.code === 'PGRST116') return null; // Not found
      throw error;
    }

    return data as T;
  }

  async findAll(options?: {
    limit?: number;
    offset?: number;
    orderBy?: keyof T;
    order?: 'asc' | 'desc';
  }): Promise<T[]> {
    let query = this.supabase.from(this.tableName).select('*');

    if (options?.orderBy) {
      query = query.order(options.orderBy as string, { 
        ascending: options.order === 'asc' 
      });
    }

    if (options?.limit) {
      query = query.limit(options.limit);
    }

    if (options?.offset) {
      query = query.range(options.offset, options.offset + (options.limit || 10) - 1);
    }

    const { data, error } = await query;

    if (error) throw error;
    return data as T[];
  }

  async create(entity: Omit<T, 'id' | 'created_at' | 'updated_at'>): Promise<T> {
    const { data, error } = await this.supabase
      .from(this.tableName)
      .insert(entity)
      .select()
      .single();

    if (error) throw error;
    return data as T;
  }

  async update(id: string, updates: Partial<T>): Promise<T> {
    const { data, error } = await this.supabase
      .from(this.tableName)
      .update(updates)
      .eq('id', id)
      .select()
      .single();

    if (error) throw error;
    return data as T;
  }

  async delete(id: string): Promise<void> {
    const { error } = await this.supabase
      .from(this.tableName)
      .delete()
      .eq('id', id);

    if (error) throw error;
  }
}

// Specific repository
export class TeamRepository extends BaseRepository<Database['public']['Tables']['teams']['Row']> {
  constructor(supabase: SupabaseClient<Database>) {
    super('teams', supabase);
  }

  async findByCity(city: string): Promise<Team[]> {
    const { data, error } = await this.supabase
      .from('teams')
      .select('*')
      .ilike('city', `%${city}%`);

    if (error) throw error;
    return data;
  }

  async findWithPlayers(teamId: string): Promise<TeamWithPlayers | null> {
    const { data, error } = await this.supabase
      .from('teams')
      .select(`
        *,
        players:team_players(
          player:players(*)
        )
      `)
      .eq('id', teamId)
      .single();

    if (error) {
      if (error.code === 'PGRST116') return null;
      throw error;
    }

    return data;
  }

  async getTeamStatistics(teamId: string): Promise<TeamStatistics> {
    const { data, error } = await this.supabase
      .from('team_statistics')
      .select('*')
      .eq('team_id', teamId)
      .single();

    if (error) throw error;
    return data;
  }
}
```

### Complex Queries with Joins

```typescript
export class MatchService {
  async getUpcomingMatches(options: {
    teamId?: string;
    limit?: number;
    includeStats?: boolean;
  }) {
    let query = supabase
      .from('matches')
      .select(`
        *,
        home_team:teams!matches_home_team_id_fkey(
          id,
          name,
          logo_url
        ),
        away_team:teams!matches_away_team_id_fkey(
          id,
          name,
          logo_url
        ),
        venue:venues(
          name,
          city,
          capacity
        )
        ${options.includeStats ? ',match_statistics(*)' : ''}
      `)
      .gte('scheduled_date', new Date().toISOString())
      .order('scheduled_date', { ascending: true });

    if (options.teamId) {
      query = query.or(`home_team_id.eq.${options.teamId},away_team_id.eq.${options.teamId}`);
    }

    if (options.limit) {
      query = query.limit(options.limit);
    }

    const { data, error } = await query;

    if (error) throw error;
    return data;
  }

  async getMatchWithDetails(matchId: string) {
    const { data, error } = await supabase
      .from('matches')
      .select(`
        *,
        home_team:teams!matches_home_team_id_fkey(*),
        away_team:teams!matches_away_team_id_fkey(*),
        venue:venues(*),
        events:match_events(
          *,
          player:players(*)
        ),
        lineups:match_lineups(
          *,
          player:players(*)
        )
      `)
      .eq('id', matchId)
      .single();

    if (error) throw error;
    return data;
  }
}
```

## Realtime Subscriptions

### Typed Realtime Events

```typescript
export class RealtimeManager {
  private channels: Map<string, RealtimeChannel> = new Map();

  subscribeToMatch(matchId: string, handlers: {
    onGoal?: (event: GoalEvent) => void;
    onCard?: (event: CardEvent) => void;
    onSubstitution?: (event: SubstitutionEvent) => void;
    onStatusChange?: (status: MatchStatus) => void;
  }) {
    const channelName = `match:${matchId}`;
    
    const channel = supabase
      .channel(channelName)
      .on(
        'postgres_changes',
        {
          event: 'INSERT',
          schema: 'public',
          table: 'match_events',
          filter: `match_id=eq.${matchId}`
        },
        (payload) => {
          const event = payload.new as MatchEvent;
          
          switch (event.type) {
            case 'goal':
              handlers.onGoal?.(event as GoalEvent);
              break;
            case 'card':
              handlers.onCard?.(event as CardEvent);
              break;
            case 'substitution':
              handlers.onSubstitution?.(event as SubstitutionEvent);
              break;
          }
        }
      )
      .on(
        'postgres_changes',
        {
          event: 'UPDATE',
          schema: 'public',
          table: 'matches',
          filter: `id=eq.${matchId}`
        },
        (payload) => {
          const match = payload.new as Match;
          handlers.onStatusChange?.(match.status);
        }
      )
      .subscribe();

    this.channels.set(channelName, channel);
    return channelName;
  }

  subscribeToTeamUpdates(teamId: string, callback: (update: TeamUpdate) => void) {
    const channel = supabase
      .channel(`team:${teamId}`)
      .on(
        'postgres_changes',
        {
          event: '*',
          schema: 'public',
          table: 'teams',
          filter: `id=eq.${teamId}`
        },
        (payload) => {
          callback({
            type: payload.eventType,
            team: payload.new as Team,
            oldTeam: payload.old as Team | null,
            timestamp: new Date()
          });
        }
      )
      .subscribe();

    this.channels.set(`team:${teamId}`, channel);
  }

  // Presence for live users
  async trackUserPresence(matchId: string, userId: string) {
    const channel = supabase.channel(`match:${matchId}:presence`);
    
    const userStatus = {
      user_id: userId,
      online_at: new Date().toISOString(),
    };

    channel
      .on('presence', { event: 'sync' }, () => {
        const state = channel.presenceState();
        console.log('Users watching:', Object.keys(state).length);
      })
      .on('presence', { event: 'join' }, ({ key, newPresences }) => {
        console.log('User joined:', key);
      })
      .on('presence', { event: 'leave' }, ({ key, leftPresences }) => {
        console.log('User left:', key);
      })
      .subscribe(async (status) => {
        if (status === 'SUBSCRIBED') {
          await channel.track(userStatus);
        }
      });

    return channel;
  }

  unsubscribe(channelName: string) {
    const channel = this.channels.get(channelName);
    if (channel) {
      supabase.removeChannel(channel);
      this.channels.delete(channelName);
    }
  }

  unsubscribeAll() {
    this.channels.forEach((channel) => {
      supabase.removeChannel(channel);
    });
    this.channels.clear();
  }
}
```

## Storage Management

### File Upload Service

```typescript
export class StorageService {
  private readonly BUCKETS = {
    AVATARS: 'avatars',
    TEAM_LOGOS: 'team-logos',
    MATCH_PHOTOS: 'match-photos',
    DOCUMENTS: 'documents'
  } as const;

  async uploadAvatar(userId: string, file: File): Promise<string> {
    // Validate file
    this.validateImageFile(file);

    // Generate unique filename
    const fileExt = file.name.split('.').pop();
    const fileName = `${userId}-${Date.now()}.${fileExt}`;
    const filePath = `${userId}/${fileName}`;

    // Upload file
    const { data, error } = await supabase.storage
      .from(this.BUCKETS.AVATARS)
      .upload(filePath, file, {
        cacheControl: '3600',
        upsert: true
      });

    if (error) throw error;

    // Update user profile with new avatar URL
    const publicUrl = this.getPublicUrl(this.BUCKETS.AVATARS, data.path);
    
    await supabase
      .from('profiles')
      .update({ avatar_url: publicUrl })
      .eq('id', userId);

    return publicUrl;
  }

  async uploadTeamLogo(teamId: string, file: File): Promise<string> {
    this.validateImageFile(file, { maxSize: 5 * 1024 * 1024 }); // 5MB

    const fileExt = file.name.split('.').pop();
    const fileName = `${teamId}-logo.${fileExt}`;

    const { data, error } = await supabase.storage
      .from(this.BUCKETS.TEAM_LOGOS)
      .upload(fileName, file, {
        cacheControl: '3600',
        upsert: true
      });

    if (error) throw error;

    return this.getPublicUrl(this.BUCKETS.TEAM_LOGOS, data.path);
  }

  async uploadMatchPhotos(matchId: string, files: File[]): Promise<string[]> {
    const uploadPromises = files.map(async (file, index) => {
      this.validateImageFile(file);

      const fileExt = file.name.split('.').pop();
      const fileName = `${matchId}/${Date.now()}-${index}.${fileExt}`;

      const { data, error } = await supabase.storage
        .from(this.BUCKETS.MATCH_PHOTOS)
        .upload(fileName, file);

      if (error) throw error;

      return this.getPublicUrl(this.BUCKETS.MATCH_PHOTOS, data.path);
    });

    return Promise.all(uploadPromises);
  }

  async deleteFile(bucket: string, path: string): Promise<void> {
    const { error } = await supabase.storage
      .from(bucket)
      .remove([path]);

    if (error) throw error;
  }

  getPublicUrl(bucket: string, path: string): string {
    const { data } = supabase.storage
      .from(bucket)
      .getPublicUrl(path);

    return data.publicUrl;
  }

  async getSignedUrl(bucket: string, path: string, expiresIn = 3600): Promise<string> {
    const { data, error } = await supabase.storage
      .from(bucket)
      .createSignedUrl(path, expiresIn);

    if (error) throw error;
    return data.signedUrl;
  }

  private validateImageFile(file: File, options?: {
    maxSize?: number;
    allowedTypes?: string[];
  }) {
    const maxSize = options?.maxSize || 10 * 1024 * 1024; // 10MB default
    const allowedTypes = options?.allowedTypes || ['image/jpeg', 'image/png', 'image/webp'];

    if (file.size > maxSize) {
      throw new Error(`File size exceeds ${maxSize / 1024 / 1024}MB limit`);
    }

    if (!allowedTypes.includes(file.type)) {
      throw new Error(`File type ${file.type} is not allowed`);
    }
  }
}
```

## Row Level Security (RLS) Helpers

### Policy Creation Patterns

```sql
-- Enable RLS on tables
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE teams ENABLE ROW LEVEL SECURITY;
ALTER TABLE matches ENABLE ROW LEVEL SECURITY;

-- Profiles policies
CREATE POLICY "Users can view all profiles"
  ON profiles FOR SELECT
  USING (true);

CREATE POLICY "Users can update own profile"
  ON profiles FOR UPDATE
  USING (auth.uid() = id)
  WITH CHECK (auth.uid() = id);

-- Teams policies
CREATE POLICY "Anyone can view teams"
  ON teams FOR SELECT
  USING (true);

CREATE POLICY "Only admins can create teams"
  ON teams FOR INSERT
  USING (
    EXISTS (
      SELECT 1 FROM profiles
      WHERE profiles.id = auth.uid()
      AND profiles.role = 'admin'
    )
  );

CREATE POLICY "Team members can update their team"
  ON teams FOR UPDATE
  USING (
    EXISTS (
      SELECT 1 FROM team_members
      WHERE team_members.team_id = teams.id
      AND team_members.user_id = auth.uid()
      AND team_members.role IN ('admin', 'coach')
    )
  );

-- Function for checking team membership
CREATE OR REPLACE FUNCTION is_team_member(team_id uuid, user_id uuid)
RETURNS boolean AS $$
BEGIN
  RETURN EXISTS (
    SELECT 1 FROM team_members
    WHERE team_members.team_id = is_team_member.team_id
    AND team_members.user_id = is_team_member.user_id
  );
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### RLS Testing in Development

```typescript
export class RLSTestHelper {
  static async testPolicyAsUser(userId: string, query: () => Promise<any>) {
    // Temporarily set the user context
    await supabaseAdmin.rpc('set_current_user', { user_id: userId });
    
    try {
      const result = await query();
      return { success: true, data: result };
    } catch (error) {
      return { success: false, error };
    } finally {
      // Reset user context
      await supabaseAdmin.rpc('clear_current_user');
    }
  }

  static async validatePolicies(tableName: string) {
    const policies = await supabaseAdmin
      .from('pg_policies')
      .select('*')
      .eq('tablename', tableName);

    console.log(`Policies for ${tableName}:`, policies.data);
    return policies.data;
  }
}
```

## Edge Functions Integration

```typescript
// Invoke Supabase Edge Function
export async function callEdgeFunction<T = any>(
  functionName: string,
  payload?: any
): Promise<T> {
  const { data, error } = await supabase.functions.invoke(functionName, {
    body: payload
  });

  if (error) throw error;
  return data;
}

// Example: Send match notification
export async function sendMatchNotification(matchId: string) {
  return callEdgeFunction('send-match-notification', {
    matchId,
    type: 'reminder',
    sendAt: new Date(Date.now() + 30 * 60 * 1000) // 30 minutes before
  });
}

// Example: Generate match report
export async function generateMatchReport(matchId: string) {
  const report = await callEdgeFunction<{ pdfUrl: string }>('generate-match-report', {
    matchId,
    includeStats: true,
    includePhotos: true
  });

  return report.pdfUrl;
}
```

## Error Handling

```typescript
export class SupabaseError extends Error {
  constructor(
    message: string,
    public code: string,
    public details?: any,
    public hint?: string
  ) {
    super(message);
    this.name = 'SupabaseError';
  }
}

export function handleSupabaseError(error: any): never {
  if (error.code === 'PGRST301') {
    throw new SupabaseError(
      'JWT expired',
      'AUTH_EXPIRED',
      error.details,
      'Please login again'
    );
  }

  if (error.code === '23505') {
    throw new SupabaseError(
      'Duplicate entry',
      'DUPLICATE_ERROR',
      error.details,
      'This record already exists'
    );
  }

  if (error.code === '23503') {
    throw new SupabaseError(
      'Foreign key violation',
      'REFERENCE_ERROR',
      error.details,
      'Referenced record does not exist'
    );
  }

  // Default error
  throw new SupabaseError(
    error.message || 'An error occurred',
    error.code || 'UNKNOWN_ERROR',
    error.details
  );
}

// Usage
export async function safeQuery<T>(
  queryFn: () => Promise<T>
): Promise<T> {
  try {
    return await queryFn();
  } catch (error) {
    handleSupabaseError(error);
  }
}
```

## Best Practices

1. **Always use typed clients** with generated types
2. **Handle auth state changes** properly
3. **Use RLS** for security, don't rely only on client-side checks
4. **Implement retry logic** for network failures
5. **Cache data appropriately** to reduce API calls
6. **Use realtime subscriptions** sparingly
7. **Batch operations** when possible
8. **Validate file uploads** before sending to storage
9. **Use transactions** for related operations
10. **Monitor rate limits** and implement backoff
11. **Use connection pooling** for server-side operations
12. **Implement proper error boundaries**
13. **Clean up subscriptions** on component unmount
14. **Use signed URLs** for temporary file access
15. **Test RLS policies** thoroughly in development