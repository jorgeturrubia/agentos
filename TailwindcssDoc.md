Installation

Install Tailwind CSS with Angular
Setting up Tailwind CSS in an Angular project.

01
Create your project
Start by creating a new Angular project if you don’t have one set up already. The most common approach is to use Angular CLI.

Terminal
ng new my-project --style css
cd my-project
02
Install Tailwind CSS
Install @tailwindcss/postcss and its peer dependencies via npm.

Terminal
npm install tailwindcss @tailwindcss/postcss postcss --force
03
Configure PostCSS Plugins
Create a .postcssrc.json file in the root of your project and add the @tailwindcss/postcss plugin to your PostCSS configuration.

.postcssrc.json
{
  "plugins": {
    "@tailwindcss/postcss": {}
  }
}
04
Import Tailwind CSS
Add an @import to ./src/styles.css that imports Tailwind CSS.

styles.css
@import "tailwindcss";
05
Start your build process
Run your build process with ng serve.

Terminal
ng serve
06
Start using Tailwind in your project
Start using Tailwind’s utility classes to style your content.

app.component.html
<h1 class="text-3xl font-bold underline">
  Hello world!
</h1>

Core concepts

Styling with utility classes
Building complex components from a constrained set of primitive utilities.

Overview
You style things with Tailwind by combining many single-purpose presentational classes (utility classes) directly in your markup:

ChitChat
You have a new message!

<div class="mx-auto flex max-w-sm items-center gap-x-4 rounded-xl bg-white p-6 shadow-lg outline outline-black/5 dark:bg-slate-800 dark:shadow-none dark:-outline-offset-1 dark:outline-white/10">
  <img class="size-12 shrink-0" src="/img/logo.svg" alt="ChitChat Logo" />
  <div>
    <div class="text-xl font-medium text-black dark:text-white">ChitChat</div>
    <p class="text-gray-500 dark:text-gray-400">You have a new message!</p>
  </div>
</div>
For example, in the UI above we've used:

The display and padding utilities (flex, shrink-0, and p-6) to control the overall layout
The max-width and margin utilities (max-w-sm and mx-auto) to constrain the card width and center it horizontally
The background-color, border-radius, and box-shadow utilities (bg-white, rounded-xl, and shadow-lg) to style the card's appearance
The width and height utilities (size-12) to set the width and height of the logo image
The gap utilities (gap-x-4) to handle the spacing between the logo and the text
The font-size, color, and font-weight utilities (text-xl, text-black, font-medium, etc.) to style the card text
Styling things this way contradicts a lot of traditional best practices, but once you try it you'll quickly notice some really important benefits:

You get things done faster — you don't spend any time coming up with class names, making decisions about selectors, or switching between HTML and CSS files, so your designs come together very fast.
Making changes feels safer — adding or removing a utility class to an element only ever affects that element, so you never have to worry about accidentally breaking something another page that's using the same CSS.
Maintaining old projects is easier — changing something just means finding that element in your project and changing the classes, not trying to remember how all of that custom CSS works that you haven't touched in six months.
Your code is more portable — since both the structure and styling live in the same place, you can easily copy and paste entire chunks of UI around, even between different projects.
Your CSS stops growing — since utility classes are so reusable, your CSS doesn't continue to grow linearly with every new feature you add to a project.
These benefits make a big difference on small projects, but they are even more valuable for teams working on long-running projects at scale.

Why not just use inline styles?
A common reaction to this approach is wondering, “isn’t this just inline styles?” and in some ways it is — you’re applying styles directly to elements instead of assigning them a class name and then styling that class.

But using utility classes has many important advantages over inline styles, for example:

Designing with constraints — using inline styles, every value is a magic number. With utilities, you’re choosing styles from a predefined design system, which makes it much easier to build visually consistent UIs.
Hover, focus, and other states — inline styles can’t target states like hover or focus, but Tailwind’s state variants make it easy to style those states with utility classes.
Media queries — you can’t use media queries in inline styles, but you can use Tailwind’s responsive variants to build fully responsive interfaces easily.
This component is fully responsive and includes a button with hover and active styles, and is built entirely with utility classes:

Woman's Face
Erin Lindford

Product Engineer

Message
<div class="flex flex-col gap-2 p-8 sm:flex-row sm:items-center sm:gap-6 sm:py-4 ...">
  <img class="mx-auto block h-24 rounded-full sm:mx-0 sm:shrink-0" src="/img/erin-lindford.jpg" alt="" />
  <div class="space-y-2 text-center sm:text-left">
    <div class="space-y-0.5">
      <p class="text-lg font-semibold text-black">Erin Lindford</p>
      <p class="font-medium text-gray-500">Product Engineer</p>
    </div>
    <button class="border-purple-200 text-purple-600 hover:border-transparent hover:bg-purple-600 hover:text-white active:bg-purple-700 ...">
      Message
    </button>
  </div>
</div>
Thinking in utility classes
Styling hover and focus states
To style an element on states like hover or focus, prefix any utility with the state you want to target, for example hover:bg-sky-700:

Hover over this button to see the background color change

Save changes
<button class="bg-sky-500 hover:bg-sky-700 ...">Save changes</button>
These prefixes are called variants in Tailwind, and they only apply the styles from a utility class when the condition for that variant matches.

Here's what the generated CSS looks like for the hover:bg-sky-700 class:

Generated CSS
.hover\:bg-sky-700 {
  &:hover {
    background-color: var(--color-sky-700);
  }
}
Notice how this class does nothing unless the element is hovered? Its only job is to provide hover styles — nothing else.

This is different from how you'd write traditional CSS, where a single class would usually provide the styles for many states:

HTML
<button class="btn">Save changes</button>
<style>
  .btn {
    background-color: var(--color-sky-500);
    &:hover {
      background-color: var(--color-sky-700);
    }
  }
</style>
You can even stack variants in Tailwind to apply a utility when multiple conditions match, like combining hover: and disabled:

<button class="bg-sky-500 disabled:hover:bg-sky-500 ...">Save changes</button>
Learn more in the documentation styling elements on hover, focus, and other states.

Media queries and breakpoints
Just like hover and focus states, you can style elements at different breakpoints by prefixing any utility with the breakpoint where you want that style to apply:

Resize this example to see the layout change

01
02
03
04
05
06
<div class="grid grid-cols-2 sm:grid-cols-3">
  <!-- ... -->
</div>
In the example above, the sm: prefix makes sure that grid-cols-3 only triggers at the sm breakpoint and above, which is 40rem out of the box:

Generated CSS
.sm\:grid-cols-3 {
  @media (width >= 40rem) {
    grid-template-columns: repeat(3, minmax(0, 1fr));
  }
}
Learn more in the responsive design documentation.

Targeting dark mode
Styling an element in dark mode is just a matter of adding the dark: prefix to any utility you want to apply when dark mode is active:

Light mode

Writes upside-down

The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.

Dark mode

Writes upside-down

The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.

<div class="bg-white dark:bg-gray-800 rounded-lg px-6 py-8 ring shadow-xl ring-gray-900/5">
  <div>
    <span class="inline-flex items-center justify-center rounded-md bg-indigo-500 p-2 shadow-lg">
      <svg
        class="h-6 w-6 text-white"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
        aria-hidden="true"
      >
        <!-- ... -->
      </svg>
    </span>
  </div>
  <h3 class="text-gray-900 dark:text-white mt-5 text-base font-medium tracking-tight ">Writes upside-down</h3>
  <p class="text-gray-500 dark:text-gray-400 mt-2 text-sm ">
    The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.
  </p>
</div>
Just like with hover states or media queries, the important thing to understand is that a single utility class will never include both the light and dark styles — you style things in dark mode by using multiple classes, one for the light mode styles and another for the dark mode styles.

Generated CSS
.dark\:bg-gray-800 {
  @media (prefers-color-scheme: dark) {
    background-color: var(--color-gray-800);
  }
}
Learn more in the dark mode documentation.

Using class composition
A lot of the time with Tailwind you'll even use multiple classes to build up the value for a single CSS property, for example adding multiple filters to an element:

HTML
<div class="blur-sm grayscale">
  <!-- ... -->
</div>
Both of these effects rely on the filter property in CSS, so Tailwind uses CSS variables to make it possible to compose these effects together:

Generated CSS
.blur-sm {
  --tw-blur: blur(var(--blur-sm));
  filter: var(--tw-blur,) var(--tw-brightness,) var(--tw-grayscale,);
}
.grayscale {
  --tw-grayscale: grayscale(100%);
  filter: var(--tw-blur,) var(--tw-brightness,) var(--tw-grayscale,);
}
The generated CSS above is slightly simplified, but the trick here is that each utility sets a CSS variable just for the effect it's meant to apply. Then the filter property looks at all of these variables, falling back to nothing if the variable hasn't been set.

Tailwind uses this same approach for gradients, shadow colors, transforms, and more.

Using arbitrary values
Many utilities in Tailwind are driven by theme variables, like bg-blue-500, text-xl, and shadow-md, which map to your underlying color palette, type scale, and shadows.

When you need to use a one-off value outside of your theme, use the special square bracket syntax for specifying arbitrary values:

HTML
<button class="bg-[#316ff6] ...">
  Sign in with Facebook
</button>
This can be useful for one-off colors outside of your color palette (like the Facebook blue above), but also when you need a complex custom value like a very specific grid:

HTML
<div class="grid grid-cols-[24rem_2.5rem_minmax(0,1fr)]">
  <!-- ... -->
</div>
It's also useful when you need to use CSS features like calc(), even if you are using your theme values:

HTML
<div class="max-h-[calc(100dvh-(--spacing(6)))]">
  <!-- ... -->
</div>
There's even a syntax for generating completely arbitrary CSS including an arbitrary property name, which can be useful for setting CSS variables:

HTML
<div class="[--gutter-width:1rem] lg:[--gutter-width:2rem]">
  <!-- ... -->
</div>
Learn more in the documentation on using arbitrary values.

How does this even work?
Tailwind CSS isn't one big static stylesheet like you might be used to with other CSS frameworks — it generates the CSS needed based on the classes you're actually using when you compile your CSS.

It does this by scanning all of the files in your project looking for any symbol that looks like it could be a class name:

Button.jsx
export default function Button({ size, children }) {
  let sizeClasses = {
    md: "px-4 py-2 rounded-md text-base",
    lg: "px-5 py-3 rounded-lg text-lg",
  }[size];
  return (
    <button type="button" className={`font-bold ${sizeClasses}`}>
      {children}
    </button>
  );
}
After it's found all of the potential classes, Tailwind generates the CSS for each one and compiles it all into one stylesheet of just the styles you actually need.

Since the CSS is generated based on the class name, Tailwind can recognize classes using arbitrary values like bg-[#316ff6] and generate the necessary CSS, even when the value isn't part of your theme.

Learn more about how this works in detecting classes in source files.

Complex selectors
Sometimes you need to style an element under a combination of conditions, for example in dark mode, at a specific breakpoint, when hovered, and when the element has a specific data attribute.

Here's an example of what that looks like with Tailwind:

HTML
<button class="dark:lg:data-current:hover:bg-indigo-600 ...">
  <!-- ... -->
</button>
Simplified CSS
@media (prefers-color-scheme: dark) and (width >= 64rem) {
  button[data-current]:hover {
    background-color: var(--color-indigo-600);
  }
}
Tailwind also supports things like group-hover, which let you style an element when a specific parent is hovered:

HTML
<a href="#" class="group rounded-lg p-8">
  <!-- ... -->
  <span class="group-hover:underline">Read more…</span>
</a>
Simplified CSS
@media (hover: hover) {
  a:hover span {
    text-decoration-line: underline;
  }
}
This group-* syntax works with other variants too, like group-focus, group-active, and many more.

For really complex scenarios (especially when styling HTML you don't control), Tailwind supports arbitrary variants which let you write any selector you want, directly in a class name:

HTML
<div class="[&>[data-active]+span]:text-blue-600 ...">
  <span data-active><!-- ... --></span>
  <span>This text will be blue</span>
</div>
Simplified CSS
div > [data-active] + span {
  color: var(--color-blue-600);
}
When to use inline styles
Inline styles are still very useful in Tailwind CSS projects, particularly when a value is coming from a dynamic source like a database or API:

branded-button.jsx
export function BrandedButton({ buttonColor, textColor, children }) {
  return (
    <button
      style={{
        backgroundColor: buttonColor,
        color: textColor,
      }}
      className="rounded-md px-3 py-1.5 font-medium"
    >
      {children}
    </button>
  );
}
You might also reach for an inline style for very complicated arbitrary values that are difficult to read when formatted as a class name:

HTML
<div class="grid-[2fr_max(0,var(--gutter-width))_calc(var(--gutter-width)+10px)]">
<div style="grid-template-columns: 2fr max(0, var(--gutter-width)) calc(var(--gutter-width) + 10px)">
  <!-- ... -->
</div>
Another useful pattern is setting CSS variables based on dynamic sources using inline styles, then referencing those variables with utility classes:

branded-button.jsx
export function BrandedButton({ buttonColor, buttonColorHover, textColor, children }) {
  return (
    <button
      style={{
        "--bg-color": buttonColor,
        "--bg-color-hover": buttonColorHover,
        "--text-color": textColor,
      }}
      className="bg-(--bg-color) text-(--text-color) hover:bg-(--bg-color-hover) ..."
    >
      {children}
    </button>
  );
}
Managing duplication
When you build entire projects with just utility classes, you'll inevitably find yourself repeating certain patterns to recreate the same design in different places.

For example, here the utility classes for each avatar image are repeated five separate times:

Contributors
204

198 others
<div>
  <div class="flex items-center space-x-2 text-base">
    <h4 class="font-semibold text-slate-900">Contributors</h4>
    <span class="bg-slate-100 px-2 py-1 text-xs font-semibold text-slate-700 ...">204</span>
  </div>
  <div class="mt-3 flex -space-x-2 overflow-hidden">
    <img class="inline-block h-12 w-12 rounded-full ring-2 ring-white" src="https://images.unsplash.com/photo-1491528323818-fdd1faba62cc?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80" alt="" />
    <img class="inline-block h-12 w-12 rounded-full ring-2 ring-white" src="https://images.unsplash.com/photo-1550525811-e5869dd03032?ixlib=rb-1.2.1&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80" alt="" />
    <img class="inline-block h-12 w-12 rounded-full ring-2 ring-white" src="https://images.unsplash.com/photo-1500648767791-00dcc994a43e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2.25&w=256&h=256&q=80" alt="" />
    <img class="inline-block h-12 w-12 rounded-full ring-2 ring-white" src="https://images.unsplash.com/photo-1472099645785-5658abf4ff4e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80" alt="" />
    <img class="inline-block h-12 w-12 rounded-full ring-2 ring-white" src="https://images.unsplash.com/photo-1517365830460-955ce3ccd263?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=facearea&facepad=2&w=256&h=256&q=80" alt="" />
  </div>
  <div class="mt-3 text-sm font-medium">
    <a href="#" class="text-blue-500">+ 198 others</a>
  </div>
</div>
Don't panic! In practice this isn't the problem you might be worried it is, and the strategies for dealing with it are things you already do every day.

Using loops
A lot of the time a design element that shows up more than once in the rendered page is only actually authored once because the actual markup is rendered in a loop.

For example, the duplicate avatars at the beginning of this guide would almost certainly be rendered in a loop in a real project:

Contributors
204

198 others
<div>
  <div class="flex items-center space-x-2 text-base">
    <h4 class="font-semibold text-slate-900">Contributors</h4>
    <span class="bg-slate-100 px-2 py-1 text-xs font-semibold text-slate-700 ...">204</span>
  </div>
  <div class="mt-3 flex -space-x-2 overflow-hidden">
    {#each contributors as user}
      <img class="inline-block h-12 w-12 rounded-full ring-2 ring-white" src={user.avatarUrl} alt={user.handle} />
    {/each}
  </div>
  <div class="mt-3 text-sm font-medium">
    <a href="#" class="text-blue-500">+ 198 others</a>
  </div>
</div>
When elements are rendered in a loop like this, the actual class list is only written once so there's no actual duplication problem to solve.

Using multi-cursor editing
When duplication is localized to a group of elements in a single file, the easiest way to deal with it is to use multi-cursor editing to quickly select and edit the class list for each element at once:

Home
Team
Projects
Reports
<nav class="flex justify-center space-x-4">
  <a href="/dashboard" class="font- rounded-lg px-3 py-2 text-gray-700 hover:bg-gray-100 hover:text-gray-900">
    Home
  </a>
  <a href="/team" class="font- rounded-lg px-3 py-2 text-gray-700 hover:bg-gray-100 hover:text-gray-900">
    Team
  </a>
  <a href="/projects" class="font- rounded-lg px-3 py-2 text-gray-700 hover:bg-gray-100 hover:text-gray-900">
    Projects
  </a>
  <a href="/reports" class="font- rounded-lg px-3 py-2 text-gray-700 hover:bg-gray-100 hover:text-gray-900">
    Reports
  </a>
</nav>
You'd be surprised at how often this ends up being the best solution. If you can quickly edit all of the duplicated class lists simultaneously, there's no benefit to introducing any additional abstraction.

Using components
If you need to reuse some styles across multiple files, the best strategy is to create a component if you're using a front-end framework like React, Svelte, or Vue, or a template partial if you're using a templating language like Blade, ERB, Twig, or Nunjucks.

Beach
Private Villa
Relaxing All-Inclusive Resort in Cancun
$299 USD per night
export function VacationCard({ img, imgAlt, eyebrow, title, pricing, url }) {
  return (
    <div>
      <img className="rounded-lg" src={img} alt={imgAlt} />
      <div className="mt-4">
        <div className="text-xs font-bold text-sky-500">{eyebrow}</div>
        <div className="mt-1 font-bold text-gray-700">
          <a href={url} className="hover:underline">
            {title}
          </a>
        </div>
        <div className="mt-2 text-sm text-gray-600">{pricing}</div>
      </div>
    </div>
  );
}
Now you can use this component in as many places as you like, while still having a single source of truth for the styles so they can easily be updated together in one place.

Using custom CSS
If you're using a templating language like ERB or Twig instead of something like React or Vue, creating a template partial for something as small as a button can feel like overkill compared to a simple CSS class like btn.

While it's highly recommended that you create proper template partials for more complex components, writing some custom CSS is totally fine when a template partial feels heavy-handed.

Here's what a btn-primary class might look like, using theme variables to keep the design consistent:


Save changes

HTML
<button class="btn-primary">Save changes</button>
CSS
@import "tailwindcss";
@layer components {
  .btn-primary {
    border-radius: calc(infinity * 1px);
    background-color: var(--color-violet-500);
    padding-inline: --spacing(5);
    padding-block: --spacing(2);
    font-weight: var(--font-weight-semibold);
    color: var(--color-white);
    box-shadow: var(--shadow-md);
    &:hover {
      @media (hover: hover) {
        background-color: var(--color-violet-700);
      }
    }
  }
}
Again though, for anything that's more complicated than just a single HTML element, we highly recommend using template partials so the styles and structure can be encapsulated in one place.

Managing style conflicts
Conflicting utility classes
When you add two classes that target the same CSS property, the class that appears later in the stylesheet wins. So in this example, the element will receive display: grid even though flex comes last in the actual class attribute:

HTML
<div class="grid flex">
  <!-- ... -->
</div>
CSS
.flex {
  display: flex;
}
.grid {
  display: grid;
}
In general, you should just never add two conflicting classes to the same element — only ever add the one you actually want to take effect:

example.jsx
export function Example({ gridLayout }) {
  return <div className={gridLayout ? "grid" : "flex"}>{/* ... */}</div>;
}
Using component-based libraries like React or Vue, this often means exposing specific props for styling customizations instead of letting consumers add extra classes from outside of a component, since those styles will often conflict.

Using the important modifier
When you really need to force a specific utility class to take effect and have no other means of managing the specificity, you can add ! to the end of the class name to make all of the declarations !important:

HTML
<div class="bg-teal-500 bg-red-500!">
  <!-- ... -->
</div>
Generated CSS
.bg-red-500\! {
  background-color: var(--color-red-500) !important;
}
.bg-teal-500 {
  background-color: var(--color-teal-500);
}
Using the important flag
If you're adding Tailwind to a project that has existing complex CSS with high specificity rules, you can use the important flag when importing Tailwind to mark all utilities as !important:

app.css
@import "tailwindcss" important;
Compiled CSS
@layer utilities {
  .flex {
    display: flex !important;
  }
  .gap-4 {
    gap: 1rem !important;
  }
  .underline {
    text-decoration-line: underline !important;
  }
}
Using the prefix option
If your project has class names that conflict with Tailwind CSS utilities, you can prefix all Tailwind-generated classes and CSS variables using the prefix option:

app.css
@import "tailwindcss" prefix(tw);
Compiled CSS
@layer theme {
  :root {
    --tw-color-red-500: oklch(0.637 0.237 25.331);
  }
}
@layer utilities {
  .tw\:text-red-500 {
    color: var(--tw-color-red-500);
  }
}
Hover, focus, and other states
Using utilities to style elements on hover, focus, and more.

Every utility class in Tailwind can be applied conditionally by adding a variant to the beginning of the class name that describes the condition you want to target.

For example, to apply the bg-sky-700 class on hover, use the hover:bg-sky-700 class:

Hover over this button to see the background color change

Save changes
<button class="bg-sky-500 hover:bg-sky-700 ...">Save changes</button>
Tailwind includes variants for just about everything you'll ever need, including:

Pseudo-classes, like :hover, :focus, :first-child, and :required
Pseudo-elements, like ::before, ::after, ::placeholder, and ::selection
Media and feature queries, like responsive breakpoints, dark mode, and prefers-reduced-motion
Attribute selectors, like [dir="rtl"] and [open]
Child selectors, like & > * and & *
These variants can even be stacked to target more specific situations, for example changing the background color in dark mode, at the medium breakpoint, on hover:

<button class="dark:md:hover:bg-fuchsia-600 ...">Save changes</button>
In this guide you'll learn about every variant available in the framework, how to use them with your own custom classes, and even how to create your own.

Pseudo-classes
:hover, :focus, and :active
Style elements on hover, focus, and active using the hover, focus, and active variants:

Try interacting with this button to see the hover, focus, and active states

Save changes
<button class="bg-violet-500 hover:bg-violet-600 focus:outline-2 focus:outline-offset-2 focus:outline-violet-500 active:bg-violet-700 ...">
  Save changes
</button>
Tailwind also includes variants for other interactive states like :visited, :focus-within, :focus-visible, and more.

See the pseudo-class reference for a complete list of available pseudo-class variants.

:first, :last, :odd, and :even
Style an element when it is the first-child or last-child using the first and last variants:


Kristen Ramos

kristen.ramos@example.com


Floyd Miles

floyd.miles@example.com


Courtney Henry

courtney.henry@example.com


Ted Fox

ted.fox@example.com

<ul role="list">
  {#each people as person}
    <!-- Remove top/bottom padding when first/last child -->
    <li class="flex py-4 first:pt-0 last:pb-0">
      <img class="h-10 w-10 rounded-full" src={person.imageUrl} alt="" />
      <div class="ml-3 overflow-hidden">
        <p class="text-sm font-medium text-gray-900 dark:text-white">{person.name}</p>
        <p class="truncate text-sm text-gray-500 dark:text-gray-400">{person.email}</p>
      </div>
    </li>
  {/each}
</ul>
You can also style an element when it's an odd or even child using the odd and even variants:

Name	Title	Email
Jane Cooper	Regional Paradigm Technician	jane.cooper@example.com
Cody Fisher	Product Directives Officer	cody.fisher@example.com
Leonard Krasner	Senior Designer	leonard.krasner@example.com
Emily Selman	VP, Hardware Engineering	emily.selman@example.com
Anna Roberts	Chief Strategy Officer	anna.roberts@example.com
<table>
  <!-- ... -->
  <tbody>
    {#each people as person}
      <!-- Use different background colors for odd and even rows -->
      <tr class="odd:bg-white even:bg-gray-50 dark:odd:bg-gray-900/50 dark:even:bg-gray-950">
        <td>{person.name}</td>
        <td>{person.title}</td>
        <td>{person.email}</td>
      </tr>
    {/each}
  </tbody>
</table>
Use the nth-* and nth-last-* variants to style children based on their position in the list:

<div class="nth-3:underline">
  <!-- ... -->
</div>
<div class="nth-last-5:underline">
  <!-- ... -->
</div>
<div class="nth-of-type-4:underline">
  <!-- ... -->
</div>
<div class="nth-last-of-type-6:underline">
  <!-- ... -->
</div>
You can pass any number you want to these by default, and use arbitrary values for more complex expressions like nth-[2n+1_of_li].

Tailwind also includes variants for other structural pseudo-classes like :only-child, :first-of-type, :empty, and more.

See the pseudo-class reference for a complete list of available pseudo-class variants.

:required and :disabled
Style form elements in different states using variants like required, invalid, and disabled:

Try making the email address valid to see the styles change

Username
tbone
Email
george@krugerindustrial.
Password
•••••
Save changes
<input
  type="text"
  value="tbone"
  disabled
  class="invalid:border-pink-500 invalid:text-pink-600 focus:border-sky-500 focus:outline focus:outline-sky-500 focus:invalid:border-pink-500 focus:invalid:outline-pink-500 disabled:border-gray-200 disabled:bg-gray-50 disabled:text-gray-500 disabled:shadow-none dark:disabled:border-gray-700 dark:disabled:bg-gray-800/20 ..."
/>
Using variants for this sort of thing can reduce the amount of conditional logic in your templates, letting you use the same set of classes regardless of what state an input is in and letting the browser apply the right styles for you.

Tailwind also includes variants for other form states like :read-only, :indeterminate, :checked, and more.

See the pseudo-class reference for a complete list of available pseudo-class variants.

:has()
Use the has-* variant to style an element based on the state or content of its descendants:

Payment method
Google Pay
Apple Pay
Credit Card
<label
  class="has-checked:bg-indigo-50 has-checked:text-indigo-900 has-checked:ring-indigo-200 dark:has-checked:bg-indigo-950 dark:has-checked:text-indigo-200 dark:has-checked:ring-indigo-900 ..."
>
  <svg fill="currentColor">
    <!-- ... -->
  </svg>
  Google Pay
  <input type="radio" class="checked:border-indigo-500 ..." />
</label>
You can use has-* with a pseudo-class, like has-[:focus], to style an element based on the state of its descendants. You can also use element selectors, like has-[img] or has-[a], to style an element based on the content of its descendants.

Styling based on the descendants of a group
If you need to style an element based on the descendants of a parent element, you can mark the parent with the group class and use the group-has-* variant to style the target element:


Spencer Sharp
Product Designer at planeteria.tech


Casey Jordan
Just happy to be here.


Alex Reed
A multidisciplinary designer, working at the intersection of art and technology.

alex-reed.com


Taylor Bailey
Pushing pixels. Slinging divs.

<div class="group ...">
  <img src="..." />
  <h4>Spencer Sharp</h4>
  <svg class="hidden group-has-[a]:block ..."><!-- ... --></svg>
  <p>Product Designer at <a href="...">planeteria.tech</a></p>
</div>
Styling based on the descendants of a peer
If you need to style an element based on the descendants of a sibling element, you can mark the sibling with the peer class and use the peer-has-* variant to style the target element:

Today



<div>
  <label class="peer ...">
    <input type="checkbox" name="todo[1]" checked />
    Create a to do list
  </label>
  <svg class="peer-has-checked:hidden ..."><!-- ... --></svg>
</div>
:not()
Use the not- variant to style an element when a condition is not true.

It's particularly powerful when combined with other pseudo-class variants, for example combining not-focus: with hover: to only apply hover styles when an element is not focused:

Try focusing on the button and then hovering over it

Save changes
<button class="bg-indigo-600 hover:not-focus:bg-indigo-700">
  <!-- ... -->
</button>
You can also combine the not- variant with media query variants like forced-colors or supports to only style an element when something about the user's environment is not true:

<div class="not-supports-[display:grid]:flex">
  <!-- ... -->
</div>
Styling based on parent state
When you need to style an element based on the state of some parent element, mark the parent with the group class, and use group-* variants like group-hover to style the target element:

Hover over the card to see both text elements change color

New project
Create a new project from a variety of starting templates.

<a href="#" class="group ...">
  <div>
    <svg class="stroke-sky-500 group-hover:stroke-white ..." fill="none" viewBox="0 0 24 24">
      <!-- ... -->
    </svg>
    <h3 class="text-gray-900 group-hover:text-white ...">New project</h3>
  </div>
  <p class="text-gray-500 group-hover:text-white ...">Create a new project from a variety of starting templates.</p>
</a>
This pattern works with every pseudo-class variant, for example group-focus, group-active, or even group-odd.

Differentiating nested groups
When nesting groups, you can style something based on the state of a specific parent group by giving that parent a unique group name using a group/{name} class, and including that name in variants using classes like group-hover/{name}:


Leslie Abbott
Co-Founder / CEO

Hector Adams
VP, Marketing

Blake Alexander
Account Coordinator
<ul role="list">
  {#each people as person}
    <li class="group/item ...">
      <!-- ... -->
      <a class="group/edit invisible group-hover/item:visible ..." href="tel:{person.phone}">
        <span class="group-hover/edit:text-gray-700 ...">Call</span>
        <svg class="group-hover/edit:translate-x-0.5 group-hover/edit:text-gray-500 ..."><!-- ... --></svg>
      </a>
    </li>
  {/each}
</ul>
Groups can be named however you like and don’t need to be configured in any way — just name your groups directly in your markup and Tailwind will automatically generate the necessary CSS.

Arbitrary groups
You can create one-off group-* variants on the fly by providing your own selector as an arbitrary value between square brackets:


HTML

Generated CSS
<div class="group is-published">
  <div class="hidden group-[.is-published]:block">
    Published
  </div>
</div>
For more control, you can use the & character to mark where .group should end up in the final selector relative to the selector you are passing in:


HTML

Generated CSS
<div class="group">
  <div class="group-[:nth-of-type(3)_&]:block">
    <!-- ... -->
  </div>
</div>
Implicit groups
The in-* variant works similarly to group except you don't need to add group to the parent element:

<div tabindex="0" class="group">
  <div class="opacity-50 group-focus:opacity-100">
<div tabindex="0">
  <div class="opacity-50 in-focus:opacity-100">
    <!-- ... -->
  </div>
</div>
The in-* variant responds to state changes in any parent, so if you want more fine-grained control you'll need to use group instead.

Styling based on sibling state
When you need to style an element based on the state of a sibling element, mark the sibling with the peer class, and use peer-* variants like peer-invalid to style the target element:

Try making the email address valid to see the warning disappear

Email
george@krugerindustrial.
Please provide a valid email address.

<form>
  <label class="block">
    <span class="...">Email</span>
    <input type="email" class="peer ..." />
    <p class="invisible peer-invalid:visible ...">Please provide a valid email address.</p>
  </label>
</form>
This makes it possible to do all sorts of neat tricks, like floating labels for example without any JS.

This pattern works with every pseudo-class variant, for example peer-focus, peer-required, and peer-disabled.

It's important to note that the peer marker can only be used on previous siblings because of how the subsequent-sibling combinator works in CSS:

Won't work, only previous siblings can be marked as peers

<label>
  <span class="peer-invalid:text-red-500 ...">Email</span>
  <input type="email" class="peer ..." />
</label>
Differentiating peers
When using multiple peers, you can style something on the state of a specific peer by giving that peer a unique name using a peer/{name} class, and including that name in variants using classes like peer-checked/{name}:


Published status
DraftPublished
Drafts are only visible to administrators.
<fieldset>
  <legend>Published status</legend>
  <input id="draft" class="peer/draft" type="radio" name="status" checked />
  <label for="draft" class="peer-checked/draft:text-sky-500">Draft</label>
  <input id="published" class="peer/published" type="radio" name="status" />
  <label for="published" class="peer-checked/published:text-sky-500">Published</label>
  <div class="hidden peer-checked/draft:block">Drafts are only visible to administrators.</div>
  <div class="hidden peer-checked/published:block">Your post will be publicly visible on your site.</div>
</fieldset>
Peers can be named however you like and don’t need to be configured in any way — just name your peers directly in your markup and Tailwind will automatically generate the necessary CSS.

Arbitrary peers
You can create one-off peer-* variants on the fly by providing your own selector as an arbitrary value between square brackets:


HTML

Generated CSS
<form>
  <label for="email">Email:</label>
  <input id="email" name="email" type="email" class="is-dirty peer" required />
  <div class="peer-[.is-dirty]:peer-required:block hidden">This field is required.</div>
  <!-- ... -->
</form>
For more control, you can use the & character to mark where .peer should end up in the final selector relative to the selector you are passing in:


HTML

Generated CSS
<div>
  <input type="text" class="peer" />
  <div class="hidden peer-[:nth-of-type(3)_&]:block">
    <!-- ... -->
  </div>
</div>
Pseudo-elements
::before and ::after
Style the ::before and ::after pseudo-elements using the before and after variants:

Email
you@example.com
<label>
  <span class="text-gray-700 after:ml-0.5 after:text-red-500 after:content-['*'] ...">Email</span>
  <input type="email" name="email" class="..." placeholder="you@example.com" />
</label>
When using these variants, Tailwind will automatically add content: '' by default so you don't have to specify it unless you want a different value:

When you look annoyed all the time, people think that you're busy.
<blockquote class="text-center text-2xl font-semibold text-gray-900 italic dark:text-white">
  When you look
  <span class="relative inline-block before:absolute before:-inset-1 before:block before:-skew-y-3 before:bg-pink-500">
    <span class="relative text-white dark:text-gray-950">annoyed</span>
  </span>
  all the time, people think that you're busy.
</blockquote>
It's worth noting that you don't really need ::before and ::after pseudo-elements for most things in Tailwind projects — it's usually simpler to just use a real HTML element.

For example, here's the same design from above but using a <span> instead of the ::before pseudo-element, which is a little easier to read and is actually less code:

<blockquote class="text-center text-2xl font-semibold text-gray-900 italic">
  When you look
  <span class="relative">
    <span class="absolute -inset-1 block -skew-y-3 bg-pink-500" aria-hidden="true"></span>
    <span class="relative text-white">annoyed</span>
  </span>
  all the time, people think that you're busy.
</blockquote>
Save before and after for situations where it's important that the content of the pseudo-element is not actually in the DOM and can't be selected by the user.

::placeholder
Style the placeholder text of any input or textarea using the placeholder variant:

Search
Search for anything...
<input
  class="placeholder:text-gray-500 placeholder:italic ..."
  placeholder="Search for anything..."
  type="text"
  name="search"
/>
::file
Style the button in file inputs using the file variant:

Current profile photo
Choose profile photoNingún archivo seleccionado
<input
  type="file"
  class="file:mr-4 file:rounded-full file:border-0 file:bg-violet-50 file:px-4 file:py-2 file:text-sm file:font-semibold file:text-violet-700 hover:file:bg-violet-100 dark:file:bg-violet-600 dark:file:text-violet-100 dark:hover:file:bg-violet-500 ..."
/>
::marker
Style the counters or bullets in lists using the marker variant:

Ingredients
5 cups chopped Porcini mushrooms
1/2 cup of olive oil
3lb of celery
<ul role="list" class="list-disc marker:text-sky-400 ...">
  <li>5 cups chopped Porcini mushrooms</li>
  <li>1/2 cup of olive oil</li>
  <li>3lb of celery</li>
</ul>
We've designed the marker variant to be inheritable, so although you can use it directly on an <li> element, you can also use it on a parent to avoid repeating yourself.

::selection
Style the active text selection using the selection variant:

Try selecting some of this text with your mouse

So I started to walk into the water. I won't lie to you boys, I was terrified. But I pressed on, and as I made my way past the breakers a strange calm came over me. I don't know if it was divine intervention or the kinship of all living things but I tell you Jerry at that moment, I was a marine biologist.

<div class="selection:bg-fuchsia-300 selection:text-fuchsia-900">
  <p>
    So I started to walk into the water. I won't lie to you boys, I was terrified. But I pressed on, and as I made my
    way past the breakers a strange calm came over me. I don't know if it was divine intervention or the kinship of all
    living things but I tell you Jerry at that moment, I <em>was</em> a marine biologist.
  </p>
</div>
We've designed the selection variant to be inheritable, so you can add it anywhere in the tree and it will be applied to all descendant elements.

This makes it easy to set the selection color to match your brand across your entire site:

<html>
  <head>
    <!-- ... -->
  </head>
  <body class="selection:bg-pink-300">
    <!-- ... -->
  </body>
</html>
::first-line and ::first-letter
Style the first line in a block of content using the first-line variant, and the first letter using the first-letter variant:

Well, let me tell you something, funny boy. Y'know that little stamp, the one that says "New York Public Library"? Well that may not mean anything to you, but that means a lot to me. One whole hell of a lot.

Sure, go ahead, laugh if you want to. I've seen your type before: Flashy, making the scene, flaunting convention. Yeah, I know what you're thinking. What's this guy making such a big stink about old library books? Well, let me give you a hint, junior.

<div class="text-gray-700">
  <p
    class="first-letter:float-left first-letter:mr-3 first-letter:text-7xl first-letter:font-bold first-letter:text-gray-900 first-line:tracking-widest first-line:uppercase"
  >
    Well, let me tell you something, funny boy. Y'know that little stamp, the one that says "New York Public Library"?
  </p>
  <p class="mt-6">Well that may not mean anything to you, but that means a lot to me. One whole hell of a lot.</p>
</div>
::backdrop
Style the backdrop of a native <dialog> element using the backdrop variant:

<dialog class="backdrop:bg-gray-50">
  <form method="dialog">
    <!-- ... -->
  </form>
</dialog>
If you're using native <dialog> elements in your project, you may also want to read about styling open/closed states using the open variant.

Media and feature queries
Responsive breakpoints
To style an element at a specific breakpoint, use responsive variants like md and lg.

For example, this will render a 3-column grid on mobile, a 4-column grid on medium-width screens, and a 6-column grid on large-width screens:

<div class="grid grid-cols-3 md:grid-cols-4 lg:grid-cols-6">
  <!-- ... -->
</div>
To style an element based on the width of a parent element instead of the viewport, use variants like @md and @lg:

<div class="@container">
  <div class="flex flex-col @md:flex-row">
    <!-- ... -->
  </div>
</div>
Check out the Responsive design documentation for an in-depth look at how these features work.

prefers-color-scheme
The prefers-color-scheme media query tells you whether the user prefers a light theme or dark theme, and is usually configured at the operating system level.

Use utilities with no variant to target light mode, and use the dark variant to provide overrides for dark mode:

Light mode

Writes upside-down
The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.

Dark mode

Writes upside-down
The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.

<div class="bg-white dark:bg-gray-900 ...">
  <!-- ... -->
  <h3 class="text-gray-900 dark:text-white ...">Writes upside-down</h3>
  <p class="text-gray-500 dark:text-gray-400 ...">
    The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.
  </p>
</div>
Check out the Dark Mode documentation for an in-depth look at how this feature works.

prefers-reduced-motion
The prefers-reduced-motion media query tells you if the user has requested that you minimize non-essential motion.

Use the motion-reduce variant to conditionally add styles when the user has requested reduced motion:

Try emulating `prefers-reduced-motion: reduce` in your developer tools to hide the spinner

Processing...
<button type="button" class="bg-indigo-500 ..." disabled>
  <svg class="animate-spin motion-reduce:hidden ..." viewBox="0 0 24 24"><!-- ... --></svg>
  Processing...
</button>
Tailwind also includes a motion-safe variant that only adds styles when the user has not requested reduced motion. This can be useful when using the motion-reduce helper would mean having to "undo" a lot of styles:

<!-- Using `motion-reduce` can mean lots of "undoing" styles -->
<button class="transition hover:-translate-y-0.5 motion-reduce:transition-none motion-reduce:hover:translate-y-0 ...">
  Save changes
</button>
<!-- Using `motion-safe` is less code in these situations -->
<button class="motion-safe:transition motion-safe:hover:-translate-x-0.5 ...">Save changes</button>
prefers-contrast
The prefers-contrast media query tells you if the user has requested more or less contrast.

Use the contrast-more variant to conditionally add styles when the user has requested more contrast:

Try emulating `prefers-contrast: more` in your developer tools to see the changes

Social Security Number
000-00-0000
We need this to steal your identity.

<label class="block">
  <span class="block text-sm font-medium text-gray-700">Social Security Number</span>
  <input
    class="border-gray-200 placeholder-gray-400 contrast-more:border-gray-400 contrast-more:placeholder-gray-500 ..."
  />
  <p class="text-gray-600 opacity-10 contrast-more:opacity-100 ...">We need this to steal your identity.</p>
</label>
Tailwind also includes a contrast-less variant you can use to conditionally add styles when the user has requested less contrast.

forced-colors
The forced-colors media query indicates if the user is using a forced colors mode. These modes override your site's colors with a user defined palette for text, backgrounds, links and buttons.

Use the forced-colors variant to conditionally add styles when the user has enabled a forced color mode:

Try emulating `forced-colors: active` in your developer tools to see the changes

Choose a theme:




<label>
  <input type="radio" class="appearance-none forced-colors:appearance-auto" />
  <p class="hidden forced-colors:block">Cyan</p>
  <div class="bg-cyan-200 forced-colors:hidden ..."></div>
  <div class="bg-cyan-500 forced-colors:hidden ..."></div>
</label>
Use the not-forced-colors variant to apply styles based when the user is not using a forced colors mode:

<div class="not-forced-colors:appearance-none ...">
  <!-- ... -->
</div>
Tailwind also includes a forced color adjust utilities to opt in and out of forced colors.

inverted-colors
Use the inverted-colors variant to conditionally add styles when the user has enabled an inverted color scheme:

<div class="shadow-xl inverted-colors:shadow-none ...">
  <!-- ... -->
</div>
pointer and any-pointer
The pointer media query tells you whether the user has a primary pointing device, like a mouse, and the accuracy of that pointing device.

Use the pointer-fine variant to target an accurate pointing device, like a mouse or trackpad, or the pointer-coarse variant to target a less accurate pointing device, like a touchscreen, which can be useful for providing larger click targets on touch devices:

Try emulating a touch device in your developer tools to see the changes


RAM
See performance specs

4 GB

8 GB

16 GB

32 GB

64 GB

128 GB
<fieldset aria-label="Choose a memory option">
  <div class="flex items-center justify-between">
    <div>RAM</div>
    <a href="#"> See performance specs </a>
  </div>
  <div class="mt-4 grid grid-cols-6 gap-2 pointer-coarse:mt-6 pointer-coarse:grid-cols-3 pointer-coarse:gap-4">
    <label class="p-2 pointer-coarse:p-4 ...">
      <input type="radio" name="memory-option" value="4 GB" className="sr-only" />
      <span>4 GB</span>
    </label>
    <!-- ... -->
  </div>
</fieldset>
While pointeronly targets the primary pointing device, any-pointer is used to target any of the pointing devices that might be available. Use the any-pointer-fine and any-pointer-coarse variants to provide different styles if at least one connected pointing device meets the criteria.

You can use pointer-none and any-pointer-none to target the absence of a pointing device.

orientation
Use the portrait and landscape variants to conditionally add styles when the viewport is in a specific orientation:

<div>
  <div class="portrait:hidden">
    <!-- ... -->
  </div>
  <div class="landscape:hidden">
    <p>This experience is designed to be viewed in landscape. Please rotate your device to view the site.</p>
  </div>
</div>
scripting
Use the noscript variant to conditionally add styles based on whether the user has scripting, such as JavaScript, enabled:

<div class="hidden noscript:block">
  <p>This experience requires JavaScript to function. Please enable JavaScript in your browser settings.</p>
</div>
print
Use the print variant to conditionally add styles that only apply when the document is being printed:

<div>
  <article class="print:hidden">
    <h1>My Secret Pizza Recipe</h1>
    <p>This recipe is a secret, and must not be shared with anyone</p>
    <!-- ... -->
  </article>
  <div class="hidden print:block">Are you seriously trying to print this? It's secret!</div>
</div>
@supports
Use the supports-[...] variant to style things based on whether a certain feature is supported in the user's browser:

<div class="flex supports-[display:grid]:grid ...">
  <!-- ... -->
</div>
Under the hood the supports-[...] variant generates @supports rules and takes anything you’d use with @supports (...) between the square brackets, like a property/value pair, and even expressions using and and or.

For terseness, if you only need to check if a property is supported (and not a specific value), you can just specify the property name:

<div class="bg-black/75 supports-backdrop-filter:bg-black/25 supports-backdrop-filter:backdrop-blur ...">
  <!-- ... -->
</div>
Use the not-supports-[...] variant to style things based on whether a certain feature is not supported in the user's browser:

<div class="not-supports-[display:grid]:flex">
  <!-- ... -->
</div>
You can configure shortcuts for common @supports rules you're using in your project by creating a new variant in the supports-* namespace:

@custom-variant supports-grid {
  @supports (display: grid) {
    @slot;
  }
}
You can then use these custom supports-* variants in your project:

<div class="supports-grid:grid">
  <!-- ... -->
</div>
@starting-style
Use the starting variant to set the appearance of an element when it is first rendered in the DOM, or transitions from display: none to visible:


<div>
  <button popovertarget="my-popover">Check for updates</button>
  <div popover id="my-popover" class="opacity-0 starting:open:opacity-0 ...">
    <!-- ... -->
  </div>
</div>
Attribute selectors
ARIA states
Use the aria-* variant to conditionally style things based on ARIA attributes.

For example, to apply the bg-sky-700 class when the aria-checked attribute is set to true, use the aria-checked:bg-sky-700 class:

<div aria-checked="true" class="bg-gray-600 aria-checked:bg-sky-700">
  <!-- ... -->
</div>
By default we've included variants for the most common boolean ARIA attributes:

Variant	CSS
aria-busy	&[aria-busy="true"]
aria-checked	&[aria-checked="true"]
aria-disabled	&[aria-disabled="true"]
aria-expanded	&[aria-expanded="true"]
aria-hidden	&[aria-hidden="true"]
aria-pressed	&[aria-pressed="true"]
aria-readonly	&[aria-readonly="true"]
aria-required	&[aria-required="true"]
aria-selected	&[aria-selected="true"]
You can customize which aria-* variants are available by creating a new variant:

@custom-variant aria-asc (&[aria-sort="ascending"]);
@custom-variant aria-desc (&[aria-sort="descending"]);
If you need to use a one-off aria variant that doesn’t make sense to include in your project, or for more complex ARIA attributes that take specific values, use square brackets to generate a property on the fly using any arbitrary value:

Invoice #
Client	Amount
#100	Pendant Publishing	$2,000.00
#101	Kruger Industrial Smoothing	$545.00
#102	J. Peterman	$10,000.25

HTML

Generated CSS
<table>
  <thead>
    <tr>
      <th
        aria-sort="ascending"
        class="aria-[sort=ascending]:bg-[url('/img/down-arrow.svg')] aria-[sort=descending]:bg-[url('/img/up-arrow.svg')]"
      >
        Invoice #
      </th>
      <!-- ... -->
    </tr>
  </thead>
  <!-- ... -->
</table>
ARIA state variants can also target parent and sibling elements using the group-aria-* and peer-aria-* variants:


HTML

Generated CSS
<table>
  <thead>
    <tr>
    <th aria-sort="ascending" class="group">
      Invoice #
      <svg class="group-aria-[sort=ascending]:rotate-0 group-aria-[sort=descending]:rotate-180"><!-- ... --></svg>
    </th>
    <!-- ... -->
    </tr>
  </thead>
  <!-- ... -->
</table>
Data attributes
Use the data-* variant to conditionally apply styles based on data attributes.

To check if a data attribute exists (and not a specific value), you can just specify the attribute name:

<!-- Will apply -->
<div data-active class="border border-gray-300 data-active:border-purple-500">
  <!-- ... -->
</div>
<!-- Will not apply -->
<div class="border border-gray-300 data-active:border-purple-500">
  <!-- ... -->
</div>
If you need to check for a specific value you may use an arbitrary value:

<!-- Will apply -->
<div data-size="large" class="data-[size=large]:p-8">
  <!-- ... -->
</div>
<!-- Will not apply -->
<div data-size="medium" class="data-[size=large]:p-8">
  <!-- ... -->
</div>
Alternatively, you can configure shortcuts for common data attributes you're using in your project by creating a new variant in the data-* namespace:

app.css
@import "tailwindcss";
@custom-variant data-checked (&[data-ui~="checked"]);
You can then use these custom data-* variants in your project:

<div data-ui="checked active" class="data-checked:underline">
  <!-- ... -->
</div>
RTL support
Use the rtl and ltr variants to conditionally add styles in right-to-left and left-to-right modes respectively when building multi-directional layouts:

Left-to-right


Tom Cook

Director of Operations

Right-to-left


تامر كرم

الرئيس التنفيذي

<div class="group flex items-center">
  <img class="h-12 w-12 shrink-0 rounded-full" src="..." alt="" />
  <div class="ltr:ml-3 rtl:mr-3">
    <p class="text-gray-700 group-hover:text-gray-900 ...">...</p>
    <p class="text-gray-500 group-hover:text-gray-700 ...">...</p>
  </div>
</div>
Remember, these variants are only useful if you are building a site that needs to support both left-to-right and right-to-left layouts. If you're building a site that only needs to support a single direction, you don't need these variants — just apply the styles that make sense for your content.

Open/closed state
Use the open variant to conditionally add styles when a <details> or <dialog> element is in an open state:

Try toggling the disclosure to see the styles change

The mug is round. The jar is round. They should call it Roundtine.

<details class="border border-transparent open:border-black/10 open:bg-gray-100 ..." open>
  <summary class="text-sm leading-6 font-semibold text-gray-900 select-none">Why do they call it Ovaltine?</summary>
  <div class="mt-3 text-sm leading-6 text-gray-600">
    <p>The mug is round. The jar is round. They should call it Roundtine.</p>
  </div>
</details>
This variant also targets the :popover-open pseudo-class for popovers:

<div>
  <button popovertarget="my-popover">Open Popover</button>
  <div popover id="my-popover" class="opacity-0 open:opacity-100 ...">
    <!-- ... -->
  </div>
</div>
Styling inert elements
The inert variant lets you style elements marked with the inert attribute:

Notification preferences


Custom



Everything
<form>
  <legend>Notification preferences</legend>
  <fieldset>
    <input type="radio" />
    <label> Custom </label>
    <fieldset inert class="inert:opacity-50">
      <!-- ... -->
    </fieldset>
    <input type="radio" />
    <label> Everything </label>
  </fieldset>
</form>
This is useful for adding visual cues that make it clear that sections of content aren't interactive.

Child selectors
Styling direct children
While it's generally preferable to put utility classes directly on child elements, you can use the * variant in situations where you need to style direct children that you don’t have control over:

Categories
Sales
Marketing
SEO
Analytics
Design
Strategy
Security
Growth
Mobile
UX/UI
<div>
  <h2>Categories<h2>
  <ul class="*:rounded-full *:border *:border-sky-100 *:bg-sky-50 *:px-2 *:py-0.5 dark:text-sky-300 dark:*:border-sky-500/15 dark:*:bg-sky-500/10 ...">
    <li>Sales</li>
    <li>Marketing</li>
    <li>SEO</li>
    <!-- ... -->
  </ul>
</div>
It's important to note that overriding a style with a utility directly on the child itself won't work since children rules are generated after the regular ones and they have the same specificity:

Won't work, children can't override styles given to them by the parent.

<ul class="*:bg-sky-50 ...">
  <li class="bg-red-50 ...">Sales</li>
  <li>Marketing</li>
  <li>SEO</li>
  <!-- ... -->
</ul>
Styling all descendants
Like *, the ** variant can be used to style children of an element. The main difference is that ** will apply styles to all descendants, not just the direct children. This is especially useful when you combine it with another variant for narrowing the thing you're selecting:


Leslie Abbott
Co-Founder / CEO

Hector Adams
VP, Marketing

Blake Alexander
Account Coordinator
<ul class="**:data-avatar:size-12 **:data-avatar:rounded-full ...">
  {#each items as item}
    <li>
      <img src={item.src} data-avatar />
      <p>{item.name}</p>
    </li>
  {/each}
</ul>
Custom variants
Using arbitrary variants
Just like arbitrary values let you use custom values with your utility classes, arbitrary variants let you write custom selector variants directly in your HTML.

Arbitrary variants are just format strings that represent the selector, wrapped in square brackets. For example, this arbitrary variant changes the cursor to grabbing when the element has the is-dragging class:


HTML

Generated CSS
<ul role="list">
  {#each items as item}
    <li class="[&.is-dragging]:cursor-grabbing">{item}</li>
  {/each}
</ul>
Arbitrary variants can be stacked with built-in variants or with each other, just like the rest of the variants in Tailwind:


HTML

Generated CSS
<ul role="list">
  {#each items as item}
    <li class="[&.is-dragging]:active:cursor-grabbing">{item}</li>
  {/each}
</ul>
If you need spaces in your selector, you can use an underscore. For example, this arbitrary variant selects all p elements within the element where you've added the class:


HTML

Generated CSS
<div class="[&_p]:mt-4">
  <p>Lorem ipsum...</p>
  <ul>
    <li>
      <p>Lorem ipsum...</p>
    </li>
    <!-- ... -->
  </ul>
</div>
You can also use at-rules like @media or @supports in arbitrary variants:


HTML

Generated CSS
<div class="flex [@supports(display:grid)]:grid">
  <!-- ... -->
</div>
With at-rule custom variants the & placeholder isn't necessary, just like when nesting with a preprocessor.

Registering a custom variant
If you find yourself using the same arbitrary variant multiple times in your project, it might be worth creating a custom variant using the @custom-variant directive:

@custom-variant theme-midnight (&:where([data-theme="midnight"] *));
Now you can use the theme-midnight:<utility> variant in your HTML:

<html data-theme="midnight">
  <button class="theme-midnight:bg-black ..."></button>
</html>
Learn more about adding custom variants in the adding custom variants documentation.

Appendix
Quick reference
A quick reference table of every single variant included in Tailwind by default.

Variant	CSS
hover	@media (hover: hover) { &:hover }
focus	&:focus
focus-within	&:focus-within
focus-visible	&:focus-visible
active	&:active
visited	&:visited
target	&:target
*	:is(& > *)
**	:is(& *)
has-[...]	&:has(...)
group-[...]	&:is(:where(.group)... *)
peer-[...]	&:is(:where(.peer)... ~ *)
in-[...]	:where(...) &
not-[...]	&:not(...)
inert	&:is([inert], [inert] *)
first	&:first-child
last	&:last-child
only	&:only-child
odd	&:nth-child(odd)
even	&:nth-child(even)
first-of-type	&:first-of-type
last-of-type	&:last-of-type
only-of-type	&:only-of-type
nth-[...]	&:nth-child(...)
nth-last-[...]	&:nth-last-child(...)
nth-of-type-[...]	&:nth-of-type(...)
nth-last-of-type-[...]	&:nth-last-of-type(...)
empty	&:empty
disabled	&:disabled
enabled	&:enabled
checked	&:checked
indeterminate	&:indeterminate
default	&:default
optional	&:optional
required	&:required
valid	&:valid
invalid	&:invalid
user-valid	&:user-valid
user-invalid	&:user-invalid
in-range	&:in-range
out-of-range	&:out-of-range
placeholder-shown	&:placeholder-shown
details-content	&:details-content
autofill	&:autofill
read-only	&:read-only
before	&::before
after	&::after
first-letter	&::first-letter
first-line	&::first-line
marker	&::marker, & *::marker
selection	&::selection
file	&::file-selector-button
backdrop	&::backdrop
placeholder	&::placeholder
sm	@media (width >= 40rem)
md	@media (width >= 48rem)
lg	@media (width >= 64rem)
xl	@media (width >= 80rem)
2xl	@media (width >= 96rem)
min-[...]	@media (width >= ...)
max-sm	@media (width < 40rem)
max-md	@media (width < 48rem)
max-lg	@media (width < 64rem)
max-xl	@media (width < 80rem)
max-2xl	@media (width < 96rem)
max-[...]	@media (width < ...)
@3xs	@container (width >= 16rem)
@2xs	@container (width >= 18rem)
@xs	@container (width >= 20rem)
@sm	@container (width >= 24rem)
@md	@container (width >= 28rem)
@lg	@container (width >= 32rem)
@xl	@container (width >= 36rem)
@2xl	@container (width >= 42rem)
@3xl	@container (width >= 48rem)
@4xl	@container (width >= 56rem)
@5xl	@container (width >= 64rem)
@6xl	@container (width >= 72rem)
@7xl	@container (width >= 80rem)
@min-[...]	@container (width >= ...)
@max-3xs	@container (width < 16rem)
@max-2xs	@container (width < 18rem)
@max-xs	@container (width < 20rem)
@max-sm	@container (width < 24rem)
@max-md	@container (width < 28rem)
@max-lg	@container (width < 32rem)
@max-xl	@container (width < 36rem)
@max-2xl	@container (width < 42rem)
@max-3xl	@container (width < 48rem)
@max-4xl	@container (width < 56rem)
@max-5xl	@container (width < 64rem)
@max-6xl	@container (width < 72rem)
@max-7xl	@container (width < 80rem)
@max-[...]	@container (width < ...)
dark	@media (prefers-color-scheme: dark)
motion-safe	@media (prefers-reduced-motion: no-preference)
motion-reduce	@media (prefers-reduced-motion: reduce)
contrast-more	@media (prefers-contrast: more)
contrast-less	@media (prefers-contrast: less)
forced-colors	@media (forced-colors: active)
inverted-colors	@media (inverted-colors: inverted)
pointer-fine	@media (pointer: fine)
pointer-coarse	@media (pointer: coarse)
pointer-none	@media (pointer: none)
any-pointer-fine	@media (any-pointer: fine)
any-pointer-coarse	@media (any-pointer: coarse)
any-pointer-none	@media (any-pointer: none)
portrait	@media (orientation: portrait)
landscape	@media (orientation: landscape)
noscript	@media (scripting: none)
print	@media print
supports-[…]	@supports (…)
aria-busy	&[aria-busy="true"]
aria-checked	&[aria-checked="true"]
aria-disabled	&[aria-disabled="true"]
aria-expanded	&[aria-expanded="true"]
aria-hidden	&[aria-hidden="true"]
aria-pressed	&[aria-pressed="true"]
aria-readonly	&[aria-readonly="true"]
aria-required	&[aria-required="true"]
aria-selected	&[aria-selected="true"]
aria-[…]	&[aria-…]
data-[…]	&[data-…]
rtl	&:where(:dir(rtl), [dir="rtl"], [dir="rtl"] *)
ltr	&:where(:dir(ltr), [dir="ltr"], [dir="ltr"] *)
open	&:is([open], :popover-open, :open)
starting	@starting-style
Pseudo-class reference
This is a comprehensive list of examples for all the pseudo-class variants included in Tailwind to complement the pseudo-classes documentation at the beginning of this guide.

:hover
Style an element when the user hovers over it with the mouse cursor using the hover variant:

<div class="bg-black hover:bg-white ...">
  <!-- ... -->
</div>
:focus
Style an element when it has focus using the focus variant:

<input class="border-gray-300 focus:border-blue-400 ..." />
:focus-within
Style an element when it or one of its descendants has focus using the focus-within variant:

<div class="focus-within:shadow-lg ...">
  <input type="text" />
</div>
:focus-visible
Style an element when it has been focused using the keyboard using the focus-visible variant:

<button class="focus-visible:outline-2 ...">Submit</button>
:active
Style an element when it is being pressed using the active variant:

<button class="bg-blue-500 active:bg-blue-600 ...">Submit</button>
:visited
Style a link when it has already been visited using the visited variant:

<a href="https://seinfeldquotes.com" class="text-blue-600 visited:text-purple-600 ..."> Inspiration </a>
:target
Style an element if its ID matches the current URL fragment using the target variant:

<div id="about" class="target:shadow-lg ...">
  <!-- ... -->
</div>
:first-child
Style an element if it's the first child using the first variant:

<ul>
  {#each people as person}
    <li class="py-4 first:pt-0 ...">
      <!-- ... -->
    </li>
  {/each}
</ul>
:last-child
Style an element if it's the last child using the last variant:

<ul>
  {#each people as person}
    <li class="py-4 last:pb-0 ...">
      <!-- ... -->
    </li>
  {/each}
</ul>
:only-child
Style an element if it's the only child using the only variant:

<ul>
  {#each people as person}
    <li class="py-4 only:py-0 ...">
      <!-- ... -->
    </li>
  {/each}
</ul>
:nth-child(odd)
Style an element if it's an oddly numbered child using the odd variant:

<table>
  {#each people as person}
    <tr class="bg-white odd:bg-gray-100 ...">
      <!-- ... -->
    </tr>
  {/each}
</table>
:nth-child(even)
Style an element if it's an evenly numbered child using the even variant:

<table>
  {#each people as person}
    <tr class="bg-white even:bg-gray-100 ...">
      <!-- ... -->
    </tr>
  {/each}
</table>
:first-of-type
Style an element if it's the first child of its type using the first-of-type variant:

<nav>
  <img src="/logo.svg" alt="Vandelay Industries" />
  {#each links as link}
    <a href="#" class="ml-2 first-of-type:ml-6 ...">
      <!-- ... -->
    </a>
  {/each}
</nav>
:last-of-type
Style an element if it's the last child of its type using the last-of-type variant:

<nav>
  <img src="/logo.svg" alt="Vandelay Industries" />
  {#each links as link}
    <a href="#" class="mr-2 last-of-type:mr-6 ...">
      <!-- ... -->
    </a>
  {/each}
  <button>More</button>
</nav>
:only-of-type
Style an element if it's the only child of its type using the only-of-type variant:

<nav>
  <img src="/logo.svg" alt="Vandelay Industries" />
  {#each links as link}
    <a href="#" class="mx-2 only-of-type:mx-6 ...">
      <!-- ... -->
    </a>
  {/each}
  <button>More</button>
</nav>
:nth-child()
Style an element at a specific position using the nth variant:

<nav>
  <img src="/logo.svg" alt="Vandelay Industries" />
  {#each links as link}
    <a href="#" class="mx-2 nth-3:mx-6 nth-[3n+1]:mx-7 ...">
      <!-- ... -->
    </a>
  {/each}
  <button>More</button>
</nav>
:nth-last-child()
Style an element at a specific position from the end using the nth-last variant:

<nav>
  <img src="/logo.svg" alt="Vandelay Industries" />
  {#each links as link}
    <a href="#" class="mx-2 nth-last-3:mx-6 nth-last-[3n+1]:mx-7 ...">
      <!-- ... -->
    </a>
  {/each}
  <button>More</button>
</nav>
:nth-of-type()
Style an element at a specific position, of the same type using the nth-of-type variant:

<nav>
  <img src="/logo.svg" alt="Vandelay Industries" />
  {#each links as link}
    <a href="#" class="mx-2 nth-of-type-3:mx-6 nth-of-type-[3n+1]:mx-7 ...">
      <!-- ... -->
    </a>
  {/each}
  <button>More</button>
</nav>
:nth-last-of-type()
Style an element at a specific position from the end, of the same type using the nth-last-of-type variant:

<nav>
  <img src="/logo.svg" alt="Vandelay Industries" />
  {#each links as link}
    <a href="#" class="mx-2 nth-last-of-type-3:mx-6 nth-last-of-type-[3n+1]:mx-7 ...">
      <!-- ... -->
    </a>
  {/each}
  <button>More</button>
</nav>
:empty
Style an element if it has no content using the empty variant:

<ul>
  {#each people as person}
    <li class="empty:hidden ...">{person.hobby}</li>
  {/each}
</ul>
:disabled
Style an input when it's disabled using the disabled variant:

<input class="disabled:opacity-75 ..." />
:enabled
Style an input when it's enabled using the enabled variant, most helpful when you only want to apply another style when an element is not disabled:

<input class="enabled:hover:border-gray-400 disabled:opacity-75 ..." />
:checked
Style a checkbox or radio button when it's checked using the checked variant:

<input type="checkbox" class="appearance-none checked:bg-blue-500 ..." />
:indeterminate
Style a checkbox or radio button in an indeterminate state using the indeterminate variant:

<input type="checkbox" class="appearance-none indeterminate:bg-gray-300 ..." />
:default
Style an option, checkbox or radio button that was the default value when the page initially loaded using the default variant:

<input type="checkbox" class="default:outline-2 ..." />
:optional
Style an input when it's optional using the optional variant:

<input class="border optional:border-red-500 ..." />
:required
Style an input when it's required using the required variant:

<input required class="border required:border-red-500 ..." />
:valid
Style an input when it's valid using the valid variant:

<input required class="border valid:border-green-500 ..." />
:invalid
Style an input when it's invalid using the invalid variant:

<input required class="border invalid:border-red-500 ..." />
:user-valid
Style an input when it's valid and the user has interacted with it, using the user-valid variant:

<input required class="border user-valid:border-green-500" />
:user-invalid
Style an input when it's invalid and the user has interacted with it, using the user-invalid variant:

<input required class="border user-invalid:border-red-500" />
:in-range
Style an input when its value is within a specified range limit using the in-range variant:

<input min="1" max="5" class="in-range:border-green-500 ..." />
:out-of-range
Style an input when its value is outside of a specified range limit using the out-of-range variant:

<input min="1" max="5" class="out-of-range:border-red-500 ..." />
:placeholder-shown
Style an input when the placeholder is shown using the placeholder-shown variant:

<input class="placeholder-shown:border-gray-500 ..." placeholder="you@example.com" />
:details-content
Style the content of a <details> element using the details-content variant:

<details class="details-content:bg-gray-100 ...">
  <summary>Details</summary>
  This is a secret.
</details>
:autofill
Style an input when it has been autofilled by the browser using the autofill variant:

<input class="autofill:bg-yellow-200 ..." />
:read-only
Style an input when it is read-only using the read-only variant:

<input class="read-only:bg-gray-100 ..." />
Responsive design
Using responsive utility variants to build adaptive user interfaces.

Overview
Every utility class in Tailwind can be applied conditionally at different breakpoints, which makes it a piece of cake to build complex responsive interfaces without ever leaving your HTML.

First, make sure you've added the viewport meta tag to the <head> of your document:

index.html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
Then to add a utility but only have it take effect at a certain breakpoint, all you need to do is prefix the utility with the breakpoint name, followed by the : character:

HTML
<!-- Width of 16 by default, 32 on medium screens, and 48 on large screens -->
<img class="w-16 md:w-32 lg:w-48" src="..." />
There are five breakpoints by default, inspired by common device resolutions:

Breakpoint prefix	Minimum width	CSS
sm	40rem (640px)	@media (width >= 40rem) { ... }
md	48rem (768px)	@media (width >= 48rem) { ... }
lg	64rem (1024px)	@media (width >= 64rem) { ... }
xl	80rem (1280px)	@media (width >= 80rem) { ... }
2xl	96rem (1536px)	@media (width >= 96rem) { ... }
This works for every utility class in the framework, which means you can change literally anything at a given breakpoint — even things like letter spacing or cursor styles.

Here's a simple example of a marketing page component that uses a stacked layout on small screens, and a side-by-side layout on larger screens:


<div class="mx-auto max-w-md overflow-hidden rounded-xl bg-white shadow-md md:max-w-2xl">
  <div class="md:flex">
    <div class="md:shrink-0">
      <img
        class="h-48 w-full object-cover md:h-full md:w-48"
        src="/img/building.jpg"
        alt="Modern building architecture"
      />
    </div>
    <div class="p-8">
      <div class="text-sm font-semibold tracking-wide text-indigo-500 uppercase">Company retreats</div>
      <a href="#" class="mt-1 block text-lg leading-tight font-medium text-black hover:underline">
        Incredible accommodation for your team
      </a>
      <p class="mt-2 text-gray-500">
        Looking to take your team away on a retreat to enjoy awesome food and take in some sunshine? We have a list of
        places to do just that.
      </p>
    </div>
  </div>
</div>
Here's how the example above works:

By default, the outer div is display: block, but by adding the md:flex utility, it becomes display: flex on medium screens and larger.
When the parent is a flex container, we want to make sure the image never shrinks, so we've added md:shrink-0 to prevent shrinking on medium screens and larger. Technically we could have just used shrink-0 since it would do nothing on smaller screens, but since it only matters on md screens, it's a good idea to make that clear in the class name.
On small screens the image is automatically full width by default. On medium screens and up, we've constrained the width to a fixed size and ensured the image is full height using md:h-full md:w-48.
We've only used one breakpoint in this example, but you could easily customize this component at other sizes using the sm, lg, xl, or 2xl responsive prefixes as well.

Working mobile-first
Tailwind uses a mobile-first breakpoint system, similar to what you might be used to in other frameworks like Bootstrap.

What this means is that unprefixed utilities (like uppercase) take effect on all screen sizes, while prefixed utilities (like md:uppercase) only take effect at the specified breakpoint and above.

Targeting mobile screens
Where this approach surprises people most often is that to style something for mobile, you need to use the unprefixed version of a utility, not the sm: prefixed version. Don't think of sm: as meaning "on small screens", think of it as "at the small breakpoint".

Don't use sm: to target mobile devices

HTML
<!-- This will only center text on screens 640px and wider, not on small screens -->
<div class="sm:text-center"></div>
Use unprefixed utilities to target mobile, and override them at larger breakpoints

HTML
<!-- This will center text on mobile, and left align it on screens 640px and wider -->
<div class="text-center sm:text-left"></div>
For this reason, it's often a good idea to implement the mobile layout for a design first, then layer on any changes that make sense for sm screens, followed by md screens, etc.

Targeting a breakpoint range
By default, styles applied by rules like md:flex will apply at that breakpoint and stay applied at larger breakpoints.

If you'd like to apply a utility only when a specific breakpoint range is active, stack a responsive variant like md with a max-* variant to limit that style to a specific range:

HTML
<div class="md:max-xl:flex">
  <!-- ... -->
</div>
Tailwind generates a corresponding max-* variant for each breakpoint, so out of the box the following variants are available:

Variant	Media query
max-sm	@media (width < 40rem) { ... }
max-md	@media (width < 48rem) { ... }
max-lg	@media (width < 64rem) { ... }
max-xl	@media (width < 80rem) { ... }
max-2xl	@media (width < 96rem) { ... }
Targeting a single breakpoint
To target a single breakpoint, target the range for that breakpoint by stacking a responsive variant like md with the max-* variant for the next breakpoint:

HTML
<div class="md:max-lg:flex">
  <!-- ... -->
</div>
Read about targeting breakpoint ranges to learn more.

Using custom breakpoints
Customizing your theme
Use the --breakpoint-* theme variables to customize your breakpoints:

app.css
@import "tailwindcss";
@theme {
  --breakpoint-xs: 30rem;
  --breakpoint-2xl: 100rem;
  --breakpoint-3xl: 120rem;
}
This updates the 2xl breakpoint to use 100rem instead of the default 96rem, and creates new xs and 3xl breakpoints that can be used in your markup:

HTML
<div class="grid xs:grid-cols-2 3xl:grid-cols-6">
  <!-- ... -->
</div>
Note that it's important to always use the same unit for defining your breakpoints or the generated utilities may be sorted in an unexpected order, causing breakpoint classes to override each other in unexpected ways.

Tailwind uses rem for the default breakpoints, so if you are adding additional breakpoints to the defaults, make sure you use rem as well.

Learn more about customizing your theme in the theme documentation.

Removing default breakpoints
To remove a default breakpoint, reset its value to the initial keyword:

app.css
@import "tailwindcss";
@theme {
  --breakpoint-2xl: initial;
}
You can also reset all of the default breakpoints using --breakpoint-*: initial, then define all of your breakpoints from scratch:

app.css
@import "tailwindcss";
@theme {
  --breakpoint-*: initial;
  --breakpoint-tablet: 40rem;
  --breakpoint-laptop: 64rem;
  --breakpoint-desktop: 80rem;
}
Learn more removing default theme values in the theme documentation.

Using arbitrary values
If you need to use a one-off breakpoint that doesn’t make sense to include in your theme, use the min or max variants to generate a custom breakpoint on the fly using any arbitrary value.

<div class="max-[600px]:bg-sky-300 min-[320px]:text-center">
  <!-- ... -->
</div>
Learn more about arbitrary value support in the arbitrary values documentation.

Container queries
What are container queries?
Container queries are a modern CSS feature that let you style something based on the size of a parent element instead of the size of the entire viewport. They let you build components that are a lot more portable and reusable because they can change based on the actual space available for that component.

Basic example
Use the @container class to mark an element as a container, then use variants like @sm and @md to style child elements based on the size of the container:

HTML
<div class="@container">
  <div class="flex flex-col @md:flex-row">
    <!-- ... -->
  </div>
</div>
Just like breakpoint variants, container queries are mobile-first in Tailwind CSS and apply at the target container size and up.

Max-width container queries
Use variants like @max-sm and @max-md to apply a style below a specific container size:

HTML
<div class="@container">
  <div class="flex flex-row @max-md:flex-col">
    <!-- ... -->
  </div>
</div>
Container query ranges
Stack a regular container query variant with a max-width container query variant to target a specific range:

HTML
<div class="@container">
  <div class="flex flex-row @sm:@max-md:flex-col">
    <!-- ... -->
  </div>
</div>
Named containers
For complex designs that use multiple nested containers, you can name containers using @container/{name} and target specific containers with variants like @sm/{name} and @md/{name}:

HTML
<div class="@container/main">
  <!-- ... -->
  <div class="flex flex-row @sm/main:flex-col">
    <!-- ... -->
  </div>
</div>
This makes it possible to style something based on the size of a distant container, rather than just the nearest container.

Using custom container sizes
Use the --container-* theme variables to customize your container sizes:

app.css
@import "tailwindcss";
@theme {
  --container-8xl: 96rem;
}
This adds a new 8xl container query variant that can be used in your markup:

HTML
<div class="@container">
  <div class="flex flex-col @8xl:flex-row">
    <!-- ... -->
  </div>
</div>
Learn more about customizing your theme in the theme documentation.

Using arbitrary values
Use variants like @min-[475px] and @max-[960px] for one-off container query sizes you don't want to add to your theme:

HTML
<div class="@container">
  <div class="flex flex-col @min-[475px]:flex-row">
    <!-- ... -->
  </div>
</div>
Using container query units
Use container query length units like cqw as arbitrary values in other utility classes to reference the container size:

HTML
<div class="@container">
  <div class="w-[50cqw]">
    <!-- ... -->
  </div>
</div>
Container size reference
By default, Tailwind includes container sizes ranging from 16rem (256px) to 80rem (1280px):

Variant	Minimum width	CSS
@3xs	16rem (256px)	@container (width >= 16rem) { … }
@2xs	18rem (288px)	@container (width >= 18rem) { … }
@xs	20rem (320px)	@container (width >= 20rem) { … }
@sm	24rem (384px)	@container (width >= 24rem) { … }
@md	28rem (448px)	@container (width >= 28rem) { … }
@lg	32rem (512px)	@container (width >= 32rem) { … }
@xl	36rem (576px)	@container (width >= 36rem) { … }
@2xl	42rem (672px)	@container (width >= 42rem) { … }
@3xl	48rem (768px)	@container (width >= 48rem) { … }
@4xl	56rem (896px)	@container (width >= 56rem) { … }
@5xl	64rem (1024px)	@container (width >= 64rem) { … }
@6xl	72rem (1152px)	@container (width >= 72rem) { … }
@7xl	80rem (1280px)	@container (width >= 80rem) { … }

Core concepts

Dark mode
Using variants to style your site in dark mode.

Overview
Now that dark mode is a first-class feature of many operating systems, it's becoming more and more common to design a dark version of your website to go along with the default design.

To make this as easy as possible, Tailwind includes a dark variant that lets you style your site differently when dark mode is enabled:

Light mode

Writes upside-down

The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.

Dark mode

Writes upside-down

The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.

<div class="bg-white dark:bg-gray-800 rounded-lg px-6 py-8 ring shadow-xl ring-gray-900/5">
  <div>
    <span class="inline-flex items-center justify-center rounded-md bg-indigo-500 p-2 shadow-lg">
      <svg class="h-6 w-6 stroke-white" ...>
        <!-- ... -->
      </svg>
    </span>
  </div>
  <h3 class="text-gray-900 dark:text-white mt-5 text-base font-medium tracking-tight ">Writes upside-down</h3>
  <p class="text-gray-500 dark:text-gray-400 mt-2 text-sm ">
    The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.
  </p>
</div>
By default this uses the prefers-color-scheme CSS media feature, but you can also build sites that support toggling dark mode manually by overriding the dark variant.

Toggling dark mode manually
If you want your dark theme to be driven by a CSS selector instead of the prefers-color-scheme media query, override the dark variant to use your custom selector:

app.css
@import "tailwindcss";
@custom-variant dark (&:where(.dark, .dark *));
Now instead of dark:* utilities being applied based on prefers-color-scheme, they will be applied whenever the dark class is present earlier in the HTML tree:

HTML
<html class="dark">
  <body>
    <div class="bg-white dark:bg-black">
      <!-- ... -->
    </div>
  </body>
</html>
How you add the dark class to the html element is up to you, but a common approach is to use a bit of JavaScript that updates the class attribute and syncs that preference to somewhere like localStorage.

Using a data attribute
To use a data attribute instead of a class to activate dark mode, just override the dark variant with an attribute selector instead:

app.css
@import "tailwindcss";
@custom-variant dark (&:where([data-theme=dark], [data-theme=dark] *));
Now dark mode utilities will be applied whenever the data-theme attribute is set to dark somewhere up the tree:

HTML
<html data-theme="dark">
  <body>
    <div class="bg-white dark:bg-black">
      <!-- ... -->
    </div>
  </body>
</html>
With system theme support
To build three-way theme toggles that support light mode, dark mode, and your system theme, use a custom dark mode selector and the window.matchMedia() API to detect the system theme and update the html element when needed.

Here's a simple example of how you can support light mode, dark mode, as well as respecting the operating system preference:

spaghetti.js
// On page load or when changing themes, best to add inline in `head` to avoid FOUC
document.documentElement.classList.toggle(
  "dark",
  localStorage.theme === "dark" ||
    (!("theme" in localStorage) && window.matchMedia("(prefers-color-scheme: dark)").matches),
);
// Whenever the user explicitly chooses light mode
localStorage.theme = "light";
// Whenever the user explicitly chooses dark mode
localStorage.theme = "dark";
// Whenever the user explicitly chooses to respect the OS preference
localStorage.removeItem("theme");
Again you can manage this however you like, even storing the preference server-side in a database and rendering the class on the server — it's totally up to you.

Core concepts

Theme variables
Using utility classes as an API for your design tokens.

Overview
Tailwind is a framework for building custom designs, and different designs need different typography, colors, shadows, breakpoints, and more.

These low-level design decisions are often called design tokens, and in Tailwind projects you store those values in theme variables.

What are theme variables?
Theme variables are special CSS variables defined using the @theme directive that influence which utility classes exist in your project.

For example, you can add a new color to your project by defining a theme variable like --color-mint-500:

app.css
@import "tailwindcss";
@theme {
  --color-mint-500: oklch(0.72 0.11 178);
}
Now you can use utility classes like bg-mint-500, text-mint-500, or fill-mint-500 in your HTML:

HTML
<div class="bg-mint-500">
  <!-- ... -->
</div>
Tailwind also generates regular CSS variables for your theme variables so you can reference your design tokens in arbitrary values or inline styles:

HTML
<div style="background-color: var(--color-mint-500)">
  <!-- ... -->
</div>
Learn more about how theme variables map to different utility classes in the theme variable namespaces documentation.

Why @theme instead of :root?
Theme variables aren't just CSS variables — they also instruct Tailwind to create new utility classes that you can use in your HTML.

Since they do more than regular CSS variables, Tailwind uses special syntax so that defining theme variables is always explicit. Theme variables are also required to be defined top-level and not nested under other selectors or media queries, and using a special syntax makes it possible to enforce that.

Defining regular CSS variables with :root can still be useful in Tailwind projects when you want to define a variable that isn't meant to be connected to a utility class. Use @theme when you want a design token to map directly to a utility class, and use :root for defining regular CSS variables that shouldn't have corresponding utility classes.

Relationship to utility classes
Some utility classes in Tailwind like flex and object-cover are static, and are always the same from project to project. But many others are driven by theme variables, and only exist because of the theme variables you've defined.

For example, theme variables defined in the --font-* namespace determine all of the font-family utilities that exist in a project:

./node_modules/tailwindcss/theme.css
@theme {
  --font-sans: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  --font-serif: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
  --font-mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  /* ... */
}
The font-sans, font-serif, and font-mono utilities only exist by default because Tailwind's default theme defines the --font-sans, --font-serif, and --font-mono theme variables.

If another theme variable like --font-poppins were defined, a font-poppins utility class would become available to go with it:

app.css
@import "tailwindcss";
@theme {
  --font-poppins: Poppins, sans-serif;
}
HTML
<h1 class="font-poppins">This headline will use Poppins.</h1>
You can name your theme variables whatever you want within these namespaces, and a corresponding utility with the same name will become available to use in your HTML.

Relationship to variants
Some theme variables are used to define variants rather than utilities. For example theme variables in the --breakpoint-* namespace determine which responsive breakpoint variants exist in your project:

app.css
@import "tailwindcss";
@theme {
  --breakpoint-3xl: 120rem;
}
Now you can use the 3xl:* variant to only trigger a utility when the viewport is 120rem or wider:

HTML
<div class="3xl:grid-cols-6 grid grid-cols-2 md:grid-cols-4">
  <!-- ... -->
</div>
Learn more about how theme variables map to different utility classes and variants in the theme variable namespaces documentation.

Theme variable namespaces
Theme variables are defined in namespaces and each namespace corresponds to one or more utility class or variant APIs.

Defining new theme variables in these namespaces will make new corresponding utilities and variants available in your project:

Namespace	Utility classes
--color-*	Color utilities like bg-red-500, text-sky-300, and many more
--font-*	Font family utilities like font-sans
--text-*	Font size utilities like text-xl
--font-weight-*	Font weight utilities like font-bold
--tracking-*	Letter spacing utilities like tracking-wide
--leading-*	Line height utilities like leading-tight
--breakpoint-*	Responsive breakpoint variants like sm:*
--container-*	Container query variants like @sm:* and size utilities like max-w-md
--spacing-*	Spacing and sizing utilities like px-4, max-h-16, and many more
--radius-*	Border radius utilities like rounded-sm
--shadow-*	Box shadow utilities like shadow-md
--inset-shadow-*	Inset box shadow utilities like inset-shadow-xs
--drop-shadow-*	Drop shadow filter utilities like drop-shadow-md
--blur-*	Blur filter utilities like blur-md
--perspective-*	Perspective utilities like perspective-near
--aspect-*	Aspect ratio utilities like aspect-video
--ease-*	Transition timing function utilities like ease-out
--animate-*	Animation utilities like animate-spin
For a list of all of the default theme variables, see the default theme variable reference.

Default theme variables
When you import tailwindcss at the top of your CSS file, it includes a set of default theme variables to get you started.

Here's what you're actually importing when you import tailwindcss:

node_modules/tailwindcss/index.css
@layer theme, base, components, utilities;
@import "./theme.css" layer(theme);
@import "./preflight.css" layer(base);
@import "./utilities.css" layer(utilities);
That theme.css file includes the default color palette, type scale, shadows, fonts, and more:

node_modules/tailwindcss/theme.css
@theme {
  --font-sans: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  --font-serif: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
  --font-mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  --color-red-50: oklch(0.971 0.013 17.38);
  --color-red-100: oklch(0.936 0.032 17.717);
  --color-red-200: oklch(0.885 0.062 18.334);
  /* ... */
  --shadow-2xs: 0 1px rgb(0 0 0 / 0.05);
  --shadow-xs: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-sm: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
  /* ... */
}
This is why utilities like bg-red-200, font-serif, and shadow-sm exist out of the box — they're driven by the default theme, not hardcoded into the framework like flex-col or pointer-events-none.

For a list of all of the default theme variables, see the default theme variable reference.

Customizing your theme
The default theme variables are very general purpose and suitable for building dramatically different designs, but they are still just a starting point. It's very common to customize things like the color palette, fonts, and shadows to build exactly the design you have in mind.

Extending the default theme
Use @theme to define new theme variables and extend the default theme:

app.css
@import "tailwindcss";
@theme {
  --font-script: Great Vibes, cursive;
}
This makes a new font-script utility class available that you can use in your HTML, just like the default font-sans or font-mono utilities:

HTML
<p class="font-script">This will use the Great Vibes font family.</p>
Learn more about how theme variables map to different utility classes and variants in the theme variable namespaces documentation.

Overriding the default theme
Override a default theme variable value by redefining it within @theme:

app.css
@import "tailwindcss";
@theme {
  --breakpoint-sm: 30rem;
}
Now the sm:* variant will trigger at 30rem instead of the default 40rem viewport size:

HTML
<div class="grid grid-cols-1 sm:grid-cols-3">
  <!-- ... -->
</div>
To completely override an entire namespace in the default theme, set the entire namespace to initial using the special asterisk syntax:

app.css
@import "tailwindcss";
@theme {
  --color-*: initial;
  --color-white: #fff;
  --color-purple: #3f3cbb;
  --color-midnight: #121063;
  --color-tahiti: #3ab7bf;
  --color-bermuda: #78dcca;
}
When you do this, all of the default utilities that use that namespace (like bg-red-500) will be removed, and only your custom values (like bg-midnight) will be available.

Learn more about how theme variables map to different utility classes and variants in the theme variable namespaces documentation.

Using a custom theme
To completely disable the default theme and use only custom values, set the global theme variable namespace to initial:

app.css
@import "tailwindcss";
@theme {
  --*: initial;
  --spacing: 4px;
  --font-body: Inter, sans-serif;
  --color-lagoon: oklch(0.72 0.11 221.19);
  --color-coral: oklch(0.74 0.17 40.24);
  --color-driftwood: oklch(0.79 0.06 74.59);
  --color-tide: oklch(0.49 0.08 205.88);
  --color-dusk: oklch(0.82 0.15 72.09);
}
Now none of the default utility classes that are driven by theme variables will be available, and you'll only be able to use utility classes matching your custom theme variables like font-body and text-dusk.

Defining animation keyframes
Define the @keyframes rules for your --animate-* theme variables within @theme to include them in your generated CSS:

app.css
@import "tailwindcss";
@theme {
  --animate-fade-in-scale: fade-in-scale 0.3s ease-out;
  @keyframes fade-in-scale {
    0% {
      opacity: 0;
      transform: scale(0.95);
    }
    100% {
      opacity: 1;
      transform: scale(1);
    }
  }
}
If you want your custom @keyframes rules to always be included even when not adding an --animate-* theme variable, define them outside of @theme instead.

Referencing other variables
When defining theme variables that reference other variables, use the inline option:

app.css
@import "tailwindcss";
@theme inline {
  --font-sans: var(--font-inter);
}
Using the inline option, the utility class will use the theme variable value instead of referencing the actual theme variable:

dist.css
.font-sans {
  font-family: var(--font-inter);
}
Without using inline, your utility classes might resolve to unexpected values because of how variables are resolved in CSS.

For example, this text will fall back to sans-serif instead of using Inter like you might expect:

HTML
<div id="parent" style="--font-sans: var(--font-inter, sans-serif);">
  <div id="child" style="--font-inter: Inter; font-family: var(--font-sans);">
    This text will use the sans-serif font, not Inter.
  </div>
</div>
This happens because var(--font-sans) is resolved where --font-sans is defined (on #parent), and --font-inter has no value there since it's not defined until deeper in the tree (on #child).

Generating all CSS variables
By default only used CSS variables will be generated in the final CSS output. If you want to always generate all CSS variables, you can use the static theme option:

app.css
@import "tailwindcss";
@theme static {
  --color-primary: var(--color-red-500);
  --color-secondary: var(--color-blue-500);
}
Sharing across projects
Since theme variables are defined in CSS, sharing them across projects is just a matter of throwing them into their own CSS file that you can import in each project:

./packages/brand/theme.css
@theme {
  --*: initial;
  --spacing: 4px;
  --font-body: Inter, sans-serif;
  --color-lagoon: oklch(0.72 0.11 221.19);
  --color-coral: oklch(0.74 0.17 40.24);
  --color-driftwood: oklch(0.79 0.06 74.59);
  --color-tide: oklch(0.49 0.08 205.88);
  --color-dusk: oklch(0.82 0.15 72.09);
}
Then you can use @import to include your theme variables in other projects:

./packages/admin/app.css
@import "tailwindcss";
@import "../brand/theme.css";
You can put shared theme variables like this in their own package in monorepo setups or even publish them to NPM and import them just like any other third-party CSS files.

Using your theme variables
All of your theme variables are turned into regular CSS variables when you compile your CSS:

dist.css
:root {
  --font-sans: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  --font-serif: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
  --font-mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  --color-red-50: oklch(0.971 0.013 17.38);
  --color-red-100: oklch(0.936 0.032 17.717);
  --color-red-200: oklch(0.885 0.062 18.334);
  /* ... */
  --shadow-2xs: 0 1px rgb(0 0 0 / 0.05);
  --shadow-xs: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-sm: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
  /* ... */
}
This makes it easy to reference all of your design tokens in any of your custom CSS or inline styles.

With custom CSS
Use your theme variables to get access to your design tokens when you're writing custom CSS that needs to use the same values:

app.css
@import "tailwindcss";
@layer components {
  .typography {
    p {
      font-size: var(--text-base);
      color: var(--color-gray-700);
    }
    h1 {
      font-size: var(--text-2xl--line-height);
      font-weight: var(--font-weight-semibold);
      color: var(--color-gray-950);
    }
    h2 {
      font-size: var(--text-xl);
      font-weight: var(--font-weight-semibold);
      color: var(--color-gray-950);
    }
  }
}
This is often useful when styling HTML you don't control, like Markdown content coming from a database or API and rendered to HTML.

With arbitrary values
Using theme variables in arbitrary values can be useful, especially in combination with the calc() function.

HTML
<div class="relative rounded-xl">
  <div class="absolute inset-px rounded-[calc(var(--radius-xl)-1px)]">
    <!-- ... -->
  </div>
  <!-- ... -->
</div>
In the above example, we're subtracting 1px from the --radius-xl value on a nested inset element to make sure it has a concentric border radius.

Referencing in JavaScript
Most of the time when you need to reference your theme variables in JS you can just use the CSS variables directly, just like any other CSS value.

For example, the popular Motion library for React lets you animate to and from CSS variable values:

JSX
<motion.div animate={{ backgroundColor: "var(--color-blue-500)" }} />
If you need access to a resolved CSS variable value in JS, you can use getComputedStyle to get the value of a theme variable on the document root:

spaghetti.js
let styles = getComputedStyle(document.documentElement);
let shadow = styles.getPropertyValue("--shadow-xl");
Default theme variable reference
For reference, here's a complete list of the theme variables included by default when you import Tailwind CSS into your project:

tailwindcss/theme.css
@theme {
  --font-sans: ui-sans-serif, system-ui, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
  --font-serif: ui-serif, Georgia, Cambria, "Times New Roman", Times, serif;
  --font-mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
  --color-red-50: oklch(97.1% 0.013 17.38);
  --color-red-100: oklch(93.6% 0.032 17.717);
  --color-red-200: oklch(88.5% 0.062 18.334);
  --color-red-300: oklch(80.8% 0.114 19.571);
  --color-red-400: oklch(70.4% 0.191 22.216);
  --color-red-500: oklch(63.7% 0.237 25.331);
  --color-red-600: oklch(57.7% 0.245 27.325);
  --color-red-700: oklch(50.5% 0.213 27.518);
  --color-red-800: oklch(44.4% 0.177 26.899);
  --color-red-900: oklch(39.6% 0.141 25.723);
  --color-red-950: oklch(25.8% 0.092 26.042);
  --color-orange-50: oklch(98% 0.016 73.684);
  --color-orange-100: oklch(95.4% 0.038 75.164);
  --color-orange-200: oklch(90.1% 0.076 70.697);
  --color-orange-300: oklch(83.7% 0.128 66.29);
  --color-orange-400: oklch(75% 0.183 55.934);
  --color-orange-500: oklch(70.5% 0.213 47.604);
  --color-orange-600: oklch(64.6% 0.222 41.116);
  --color-orange-700: oklch(55.3% 0.195 38.402);
  --color-orange-800: oklch(47% 0.157 37.304);
  --color-orange-900: oklch(40.8% 0.123 38.172);
  --color-orange-950: oklch(26.6% 0.079 36.259);
  --color-amber-50: oklch(98.7% 0.022 95.277);
  --color-amber-100: oklch(96.2% 0.059 95.617);
  --color-amber-200: oklch(92.4% 0.12 95.746);
  --color-amber-300: oklch(87.9% 0.169 91.605);
  --color-amber-400: oklch(82.8% 0.189 84.429);
  --color-amber-500: oklch(76.9% 0.188 70.08);
  --color-amber-600: oklch(66.6% 0.179 58.318);
  --color-amber-700: oklch(55.5% 0.163 48.998);
  --color-amber-800: oklch(47.3% 0.137 46.201);
  --color-amber-900: oklch(41.4% 0.112 45.904);
  --color-amber-950: oklch(27.9% 0.077 45.635);
  --color-yellow-50: oklch(98.7% 0.026 102.212);
  --color-yellow-100: oklch(97.3% 0.071 103.193);
  --color-yellow-200: oklch(94.5% 0.129 101.54);
  --color-yellow-300: oklch(90.5% 0.182 98.111);
  --color-yellow-400: oklch(85.2% 0.199 91.936);
  --color-yellow-500: oklch(79.5% 0.184 86.047);
  --color-yellow-600: oklch(68.1% 0.162 75.834);
  --color-yellow-700: oklch(55.4% 0.135 66.442);
  --color-yellow-800: oklch(47.6% 0.114 61.907);
  --color-yellow-900: oklch(42.1% 0.095 57.708);
  --color-yellow-950: oklch(28.6% 0.066 53.813);
  --color-lime-50: oklch(98.6% 0.031 120.757);
  --color-lime-100: oklch(96.7% 0.067 122.328);
  --color-lime-200: oklch(93.8% 0.127 124.321);
  --color-lime-300: oklch(89.7% 0.196 126.665);
  --color-lime-400: oklch(84.1% 0.238 128.85);
  --color-lime-500: oklch(76.8% 0.233 130.85);
  --color-lime-600: oklch(64.8% 0.2 131.684);
  --color-lime-700: oklch(53.2% 0.157 131.589);
  --color-lime-800: oklch(45.3% 0.124 130.933);
  --color-lime-900: oklch(40.5% 0.101 131.063);
  --color-lime-950: oklch(27.4% 0.072 132.109);
  --color-green-50: oklch(98.2% 0.018 155.826);
  --color-green-100: oklch(96.2% 0.044 156.743);
  --color-green-200: oklch(92.5% 0.084 155.995);
  --color-green-300: oklch(87.1% 0.15 154.449);
  --color-green-400: oklch(79.2% 0.209 151.711);
  --color-green-500: oklch(72.3% 0.219 149.579);
  --color-green-600: oklch(62.7% 0.194 149.214);
  --color-green-700: oklch(52.7% 0.154 150.069);
  --color-green-800: oklch(44.8% 0.119 151.328);
  --color-green-900: oklch(39.3% 0.095 152.535);
  --color-green-950: oklch(26.6% 0.065 152.934);
  --color-emerald-50: oklch(97.9% 0.021 166.113);
  --color-emerald-100: oklch(95% 0.052 163.051);
  --color-emerald-200: oklch(90.5% 0.093 164.15);
  --color-emerald-300: oklch(84.5% 0.143 164.978);
  --color-emerald-400: oklch(76.5% 0.177 163.223);
  --color-emerald-500: oklch(69.6% 0.17 162.48);
  --color-emerald-600: oklch(59.6% 0.145 163.225);
  --color-emerald-700: oklch(50.8% 0.118 165.612);
  --color-emerald-800: oklch(43.2% 0.095 166.913);
  --color-emerald-900: oklch(37.8% 0.077 168.94);
  --color-emerald-950: oklch(26.2% 0.051 172.552);
  --color-teal-50: oklch(98.4% 0.014 180.72);
  --color-teal-100: oklch(95.3% 0.051 180.801);
  --color-teal-200: oklch(91% 0.096 180.426);
  --color-teal-300: oklch(85.5% 0.138 181.071);
  --color-teal-400: oklch(77.7% 0.152 181.912);
  --color-teal-500: oklch(70.4% 0.14 182.503);
  --color-teal-600: oklch(60% 0.118 184.704);
  --color-teal-700: oklch(51.1% 0.096 186.391);
  --color-teal-800: oklch(43.7% 0.078 188.216);
  --color-teal-900: oklch(38.6% 0.063 188.416);
  --color-teal-950: oklch(27.7% 0.046 192.524);
  --color-cyan-50: oklch(98.4% 0.019 200.873);
  --color-cyan-100: oklch(95.6% 0.045 203.388);
  --color-cyan-200: oklch(91.7% 0.08 205.041);
  --color-cyan-300: oklch(86.5% 0.127 207.078);
  --color-cyan-400: oklch(78.9% 0.154 211.53);
  --color-cyan-500: oklch(71.5% 0.143 215.221);
  --color-cyan-600: oklch(60.9% 0.126 221.723);
  --color-cyan-700: oklch(52% 0.105 223.128);
  --color-cyan-800: oklch(45% 0.085 224.283);
  --color-cyan-900: oklch(39.8% 0.07 227.392);
  --color-cyan-950: oklch(30.2% 0.056 229.695);
  --color-sky-50: oklch(97.7% 0.013 236.62);
  --color-sky-100: oklch(95.1% 0.026 236.824);
  --color-sky-200: oklch(90.1% 0.058 230.902);
  --color-sky-300: oklch(82.8% 0.111 230.318);
  --color-sky-400: oklch(74.6% 0.16 232.661);
  --color-sky-500: oklch(68.5% 0.169 237.323);
  --color-sky-600: oklch(58.8% 0.158 241.966);
  --color-sky-700: oklch(50% 0.134 242.749);
  --color-sky-800: oklch(44.3% 0.11 240.79);
  --color-sky-900: oklch(39.1% 0.09 240.876);
  --color-sky-950: oklch(29.3% 0.066 243.157);
  --color-blue-50: oklch(97% 0.014 254.604);
  --color-blue-100: oklch(93.2% 0.032 255.585);
  --color-blue-200: oklch(88.2% 0.059 254.128);
  --color-blue-300: oklch(80.9% 0.105 251.813);
  --color-blue-400: oklch(70.7% 0.165 254.624);
  --color-blue-500: oklch(62.3% 0.214 259.815);
  --color-blue-600: oklch(54.6% 0.245 262.881);
  --color-blue-700: oklch(48.8% 0.243 264.376);
  --color-blue-800: oklch(42.4% 0.199 265.638);
  --color-blue-900: oklch(37.9% 0.146 265.522);
  --color-blue-950: oklch(28.2% 0.091 267.935);
  --color-indigo-50: oklch(96.2% 0.018 272.314);
  --color-indigo-100: oklch(93% 0.034 272.788);
  --color-indigo-200: oklch(87% 0.065 274.039);
  --color-indigo-300: oklch(78.5% 0.115 274.713);
  --color-indigo-400: oklch(67.3% 0.182 276.935);
  --color-indigo-500: oklch(58.5% 0.233 277.117);
  --color-indigo-600: oklch(51.1% 0.262 276.966);
  --color-indigo-700: oklch(45.7% 0.24 277.023);
  --color-indigo-800: oklch(39.8% 0.195 277.366);
  --color-indigo-900: oklch(35.9% 0.144 278.697);
  --color-indigo-950: oklch(25.7% 0.09 281.288);
  --color-violet-50: oklch(96.9% 0.016 293.756);
  --color-violet-100: oklch(94.3% 0.029 294.588);
  --color-violet-200: oklch(89.4% 0.057 293.283);
  --color-violet-300: oklch(81.1% 0.111 293.571);
  --color-violet-400: oklch(70.2% 0.183 293.541);
  --color-violet-500: oklch(60.6% 0.25 292.717);
  --color-violet-600: oklch(54.1% 0.281 293.009);
  --color-violet-700: oklch(49.1% 0.27 292.581);
  --color-violet-800: oklch(43.2% 0.232 292.759);
  --color-violet-900: oklch(38% 0.189 293.745);
  --color-violet-950: oklch(28.3% 0.141 291.089);
  --color-purple-50: oklch(97.7% 0.014 308.299);
  --color-purple-100: oklch(94.6% 0.033 307.174);
  --color-purple-200: oklch(90.2% 0.063 306.703);
  --color-purple-300: oklch(82.7% 0.119 306.383);
  --color-purple-400: oklch(71.4% 0.203 305.504);
  --color-purple-500: oklch(62.7% 0.265 303.9);
  --color-purple-600: oklch(55.8% 0.288 302.321);
  --color-purple-700: oklch(49.6% 0.265 301.924);
  --color-purple-800: oklch(43.8% 0.218 303.724);
  --color-purple-900: oklch(38.1% 0.176 304.987);
  --color-purple-950: oklch(29.1% 0.149 302.717);
  --color-fuchsia-50: oklch(97.7% 0.017 320.058);
  --color-fuchsia-100: oklch(95.2% 0.037 318.852);
  --color-fuchsia-200: oklch(90.3% 0.076 319.62);
  --color-fuchsia-300: oklch(83.3% 0.145 321.434);
  --color-fuchsia-400: oklch(74% 0.238 322.16);
  --color-fuchsia-500: oklch(66.7% 0.295 322.15);
  --color-fuchsia-600: oklch(59.1% 0.293 322.896);
  --color-fuchsia-700: oklch(51.8% 0.253 323.949);
  --color-fuchsia-800: oklch(45.2% 0.211 324.591);
  --color-fuchsia-900: oklch(40.1% 0.17 325.612);
  --color-fuchsia-950: oklch(29.3% 0.136 325.661);
  --color-pink-50: oklch(97.1% 0.014 343.198);
  --color-pink-100: oklch(94.8% 0.028 342.258);
  --color-pink-200: oklch(89.9% 0.061 343.231);
  --color-pink-300: oklch(82.3% 0.12 346.018);
  --color-pink-400: oklch(71.8% 0.202 349.761);
  --color-pink-500: oklch(65.6% 0.241 354.308);
  --color-pink-600: oklch(59.2% 0.249 0.584);
  --color-pink-700: oklch(52.5% 0.223 3.958);
  --color-pink-800: oklch(45.9% 0.187 3.815);
  --color-pink-900: oklch(40.8% 0.153 2.432);
  --color-pink-950: oklch(28.4% 0.109 3.907);
  --color-rose-50: oklch(96.9% 0.015 12.422);
  --color-rose-100: oklch(94.1% 0.03 12.58);
  --color-rose-200: oklch(89.2% 0.058 10.001);
  --color-rose-300: oklch(81% 0.117 11.638);
  --color-rose-400: oklch(71.2% 0.194 13.428);
  --color-rose-500: oklch(64.5% 0.246 16.439);
  --color-rose-600: oklch(58.6% 0.253 17.585);
  --color-rose-700: oklch(51.4% 0.222 16.935);
  --color-rose-800: oklch(45.5% 0.188 13.697);
  --color-rose-900: oklch(41% 0.159 10.272);
  --color-rose-950: oklch(27.1% 0.105 12.094);
  --color-slate-50: oklch(98.4% 0.003 247.858);
  --color-slate-100: oklch(96.8% 0.007 247.896);
  --color-slate-200: oklch(92.9% 0.013 255.508);
  --color-slate-300: oklch(86.9% 0.022 252.894);
  --color-slate-400: oklch(70.4% 0.04 256.788);
  --color-slate-500: oklch(55.4% 0.046 257.417);
  --color-slate-600: oklch(44.6% 0.043 257.281);
  --color-slate-700: oklch(37.2% 0.044 257.287);
  --color-slate-800: oklch(27.9% 0.041 260.031);
  --color-slate-900: oklch(20.8% 0.042 265.755);
  --color-slate-950: oklch(12.9% 0.042 264.695);
  --color-gray-50: oklch(98.5% 0.002 247.839);
  --color-gray-100: oklch(96.7% 0.003 264.542);
  --color-gray-200: oklch(92.8% 0.006 264.531);
  --color-gray-300: oklch(87.2% 0.01 258.338);
  --color-gray-400: oklch(70.7% 0.022 261.325);
  --color-gray-500: oklch(55.1% 0.027 264.364);
  --color-gray-600: oklch(44.6% 0.03 256.802);
  --color-gray-700: oklch(37.3% 0.034 259.733);
  --color-gray-800: oklch(27.8% 0.033 256.848);
  --color-gray-900: oklch(21% 0.034 264.665);
  --color-gray-950: oklch(13% 0.028 261.692);
  --color-zinc-50: oklch(98.5% 0 0);
  --color-zinc-100: oklch(96.7% 0.001 286.375);
  --color-zinc-200: oklch(92% 0.004 286.32);
  --color-zinc-300: oklch(87.1% 0.006 286.286);
  --color-zinc-400: oklch(70.5% 0.015 286.067);
  --color-zinc-500: oklch(55.2% 0.016 285.938);
  --color-zinc-600: oklch(44.2% 0.017 285.786);
  --color-zinc-700: oklch(37% 0.013 285.805);
  --color-zinc-800: oklch(27.4% 0.006 286.033);
  --color-zinc-900: oklch(21% 0.006 285.885);
  --color-zinc-950: oklch(14.1% 0.005 285.823);
  --color-neutral-50: oklch(98.5% 0 0);
  --color-neutral-100: oklch(97% 0 0);
  --color-neutral-200: oklch(92.2% 0 0);
  --color-neutral-300: oklch(87% 0 0);
  --color-neutral-400: oklch(70.8% 0 0);
  --color-neutral-500: oklch(55.6% 0 0);
  --color-neutral-600: oklch(43.9% 0 0);
  --color-neutral-700: oklch(37.1% 0 0);
  --color-neutral-800: oklch(26.9% 0 0);
  --color-neutral-900: oklch(20.5% 0 0);
  --color-neutral-950: oklch(14.5% 0 0);
  --color-stone-50: oklch(98.5% 0.001 106.423);
  --color-stone-100: oklch(97% 0.001 106.424);
  --color-stone-200: oklch(92.3% 0.003 48.717);
  --color-stone-300: oklch(86.9% 0.005 56.366);
  --color-stone-400: oklch(70.9% 0.01 56.259);
  --color-stone-500: oklch(55.3% 0.013 58.071);
  --color-stone-600: oklch(44.4% 0.011 73.639);
  --color-stone-700: oklch(37.4% 0.01 67.558);
  --color-stone-800: oklch(26.8% 0.007 34.298);
  --color-stone-900: oklch(21.6% 0.006 56.043);
  --color-black: #000;
  --color-white: #fff;
  --spacing: 0.25rem;
  --breakpoint-sm: 40rem;
  --breakpoint-md: 48rem;
  --breakpoint-lg: 64rem;
  --breakpoint-xl: 80rem;
  --breakpoint-2xl: 96rem;
  --container-3xs: 16rem;
  --container-2xs: 18rem;
  --container-xs: 20rem;
  --container-sm: 24rem;
  --container-md: 28rem;
  --container-lg: 32rem;
  --container-xl: 36rem;
  --container-2xl: 42rem;
  --container-3xl: 48rem;
  --container-4xl: 56rem;
  --container-5xl: 64rem;
  --container-6xl: 72rem;
  --container-7xl: 80rem;
  --text-xs: 0.75rem;
  --text-xs--line-height: calc(1 / 0.75);
  --text-sm: 0.875rem;
  --text-sm--line-height: calc(1.25 / 0.875);
  --text-base: 1rem;
  --text-base--line-height: calc(1.5 / 1);
  --text-lg: 1.125rem;
  --text-lg--line-height: calc(1.75 / 1.125);
  --text-xl: 1.25rem;
  --text-xl--line-height: calc(1.75 / 1.25);
  --text-2xl: 1.5rem;
  --text-2xl--line-height: calc(2 / 1.5);
  --text-3xl: 1.875rem;
  --text-3xl--line-height: calc(2.25 / 1.875);
  --text-4xl: 2.25rem;
  --text-4xl--line-height: calc(2.5 / 2.25);
  --text-5xl: 3rem;
  --text-5xl--line-height: 1;
  --text-6xl: 3.75rem;
  --text-6xl--line-height: 1;
  --text-7xl: 4.5rem;
  --text-7xl--line-height: 1;
  --text-8xl: 6rem;
  --text-8xl--line-height: 1;
  --text-9xl: 8rem;
  --text-9xl--line-height: 1;
  --font-weight-thin: 100;
  --font-weight-extralight: 200;
  --font-weight-light: 300;
  --font-weight-normal: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;
  --font-weight-extrabold: 800;
  --font-weight-black: 900;
  --tracking-tighter: -0.05em;
  --tracking-tight: -0.025em;
  --tracking-normal: 0em;
  --tracking-wide: 0.025em;
  --tracking-wider: 0.05em;
  --tracking-widest: 0.1em;
  --leading-tight: 1.25;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --leading-loose: 2;
  --radius-xs: 0.125rem;
  --radius-sm: 0.25rem;
  --radius-md: 0.375rem;
  --radius-lg: 0.5rem;
  --radius-xl: 0.75rem;
  --radius-2xl: 1rem;
  --radius-3xl: 1.5rem;
  --radius-4xl: 2rem;
  --shadow-2xs: 0 1px rgb(0 0 0 / 0.05);
  --shadow-xs: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-sm: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
  --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
  --shadow-xl: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1);
  --shadow-2xl: 0 25px 50px -12px rgb(0 0 0 / 0.25);
  --inset-shadow-2xs: inset 0 1px rgb(0 0 0 / 0.05);
  --inset-shadow-xs: inset 0 1px 1px rgb(0 0 0 / 0.05);
  --inset-shadow-sm: inset 0 2px 4px rgb(0 0 0 / 0.05);
  --drop-shadow-xs: 0 1px 1px rgb(0 0 0 / 0.05);
  --drop-shadow-sm: 0 1px 2px rgb(0 0 0 / 0.15);
  --drop-shadow-md: 0 3px 3px rgb(0 0 0 / 0.12);
  --drop-shadow-lg: 0 4px 4px rgb(0 0 0 / 0.15);
  --drop-shadow-xl: 0 9px 7px rgb(0 0 0 / 0.1);
  --drop-shadow-2xl: 0 25px 25px rgb(0 0 0 / 0.15);
  --text-shadow-2xs: 0px 1px 0px rgb(0 0 0 / 0.15);
  --text-shadow-xs: 0px 1px 1px rgb(0 0 0 / 0.2);
  --text-shadow-sm:
    0px 1px 0px rgb(0 0 0 / 0.075), 0px 1px 1px rgb(0 0 0 / 0.075), 0px 2px 2px rgb(0 0 0 / 0.075);
  --text-shadow-md:
    0px 1px 1px rgb(0 0 0 / 0.1), 0px 1px 2px rgb(0 0 0 / 0.1), 0px 2px 4px rgb(0 0 0 / 0.1);
  --text-shadow-lg:
    0px 1px 2px rgb(0 0 0 / 0.1), 0px 3px 2px rgb(0 0 0 / 0.1), 0px 4px 8px rgb(0 0 0 / 0.1);
  --blur-xs: 4px;
  --blur-sm: 8px;
  --blur-md: 12px;
  --blur-lg: 16px;
  --blur-xl: 24px;
  --blur-2xl: 40px;
  --blur-3xl: 64px;
  --perspective-dramatic: 100px;
  --perspective-near: 300px;
  --perspective-normal: 500px;
  --perspective-midrange: 800px;
  --perspective-distant: 1200px;
  --aspect-video: 16 / 9;
  --ease-in: cubic-bezier(0.4, 0, 1, 1);
  --ease-out: cubic-bezier(0, 0, 0.2, 1);
  --ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
  --animate-spin: spin 1s linear infinite;
  --animate-ping: ping 1s cubic-bezier(0, 0, 0.2, 1) infinite;
  --animate-pulse: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
  --animate-bounce: bounce 1s infinite;
  @keyframes spin {
    to {
      transform: rotate(360deg);
    }
  }
  @keyframes ping {
    75%,
    100% {
      transform: scale(2);
      opacity: 0;
    }
  }
  @keyframes pulse {
    50% {
      opacity: 0.5;
    }
  }
  @keyframes bounce {
    0%,
    100% {
      transform: translateY(-25%);
      animation-timing-function: cubic-bezier(0.8, 0, 1, 1);
    }
    50% {
      transform: none;
      animation-timing-function: cubic-bezier(0, 0, 0.2, 1);
    }
  }
}

Core concepts

Colors
Using and customizing the color palette in Tailwind CSS projects.

Tailwind CSS includes a vast, beautiful color palette out of the box, carefully crafted by expert designers and suitable for a wide range of different design styles.

 
50
100
200
300
400
500
600
700
800
900
950
red


orange


amber


yellow


lime


green


emerald


teal


cyan


sky


blue


indigo


violet


purple


fuchsia


pink


rose


slate


gray


zinc


neutral


stone


Click to copy the OKLCH value or shift+click to copy the nearest hex value.
Every color in the default palette includes 11 steps, with 50 being the lightest, and 950 being the darkest:

50

100

200

300

400

500

600

700

800

900

950

<div>
  <div class="bg-sky-50"></div>
  <div class="bg-sky-100"></div>
  <div class="bg-sky-200"></div>
  <div class="bg-sky-300"></div>
  <div class="bg-sky-400"></div>
  <div class="bg-sky-500"></div>
  <div class="bg-sky-600"></div>
  <div class="bg-sky-700"></div>
  <div class="bg-sky-800"></div>
  <div class="bg-sky-900"></div>
  <div class="bg-sky-950"></div>
</div>
The entire color palette is available across all color related utilities, including things like background color, border color, fill, caret color, and many more.

Working with colors
Using color utilities
Use color utilities like bg-white, border-pink-300, and text-gray-950 to set the different color properties of elements in your design:

Tom Watson mentioned you in Logo redesign

9:37am

<div class="flex items-center gap-4 rounded-lg bg-white p-6 shadow-md outline outline-black/5 dark:bg-gray-800">
  <span class="inline-flex shrink-0 rounded-full border border-pink-300 bg-pink-100 p-2 dark:border-pink-300/10 dark:bg-pink-400/10">
    <svg class="size-6 stroke-pink-700 dark:stroke-pink-500"><!-- ... --></svg>
  </span>
  <div>
    <p class="text-gray-700 dark:text-gray-400">
      <span class="font-medium text-gray-950 dark:text-white">Tom Watson</span> mentioned you in
      <span class="font-medium text-gray-950 dark:text-white">Logo redesign</span>
    </p>
    <time class="mt-1 block text-gray-500" datetime="9:37">9:37am</time>
  </div>
</div>
Here's a full list of utilities that use your color palette:

Utility	Description
bg-*	Sets the background color of an element
text-*	Sets the text color of an element
decoration-*	Sets the text decoration color of an element
border-*	Sets the border color of an element
outline-*	Sets the outline color of an element
shadow-*	Sets the color of box shadows
inset-shadow-*	Sets the color of inset box shadows
ring-*	Sets the color of ring shadows
inset-ring-*	Sets the color of inset ring shadows
accent-*	Sets the accent color of form controls
caret-*	Sets the caret color in form controls
fill-*	Sets the fill color of SVG elements
stroke-*	Sets the stroke color of SVG elements
Adjusting opacity
You can adjust the opacity of a color using syntax like bg-black/75, where 75 sets the alpha channel of the color to 75%:

<div>
  <div class="bg-sky-500/10"></div>
  <div class="bg-sky-500/20"></div>
  <div class="bg-sky-500/30"></div>
  <div class="bg-sky-500/40"></div>
  <div class="bg-sky-500/50"></div>
  <div class="bg-sky-500/60"></div>
  <div class="bg-sky-500/70"></div>
  <div class="bg-sky-500/80"></div>
  <div class="bg-sky-500/90"></div>
  <div class="bg-sky-500/100"></div>
</div>
This syntax also supports arbitrary values and the CSS variable shorthand:

HTML
<div class="bg-pink-500/[71.37%]"><!-- ... --></div>
<div class="bg-cyan-400/(--my-alpha-value)"><!-- ... --></div>
Targeting dark mode
Use the dark variant to write classes like dark:bg-gray-800 that only apply a color when dark mode is active:

Light mode

Writes upside-down

The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.

Dark mode

Writes upside-down

The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.

<div class="bg-white dark:bg-gray-800 rounded-lg px-6 py-8 ring shadow-xl ring-gray-900/5">
  <div>
    <span class="inline-flex items-center justify-center rounded-md bg-indigo-500 p-2 shadow-lg">
      <svg class="h-6 w-6 stroke-white" ...>
        <!-- ... -->
      </svg>
    </span>
  </div>
  <h3 class="text-gray-900 dark:text-white mt-5 text-base font-medium tracking-tight ">Writes upside-down</h3>
  <p class="text-gray-500 dark:text-gray-400 mt-2 text-sm ">
    The Zero Gravity Pen can be used to write in any orientation, including upside-down. It even works in outer space.
  </p>
</div>
Learn more about styling for dark mode in the dark mode documentation.

Referencing in CSS
Colors are exposed as CSS variables in the --color-* namespace, so you can reference them in CSS with variables like --color-blue-500 and --color-pink-700:

CSS
@import "tailwindcss";
@layer components {
  .typography {
    color: var(--color-gray-950);
    a {
      color: var(--color-blue-500);
      &:hover {
        color: var(--color-blue-800);
      }
    }
  }
}
You can also use these as arbitrary values in utility classes:

HTML
<div class="bg-[light-dark(var(--color-white),var(--color-gray-950))]">
  <!-- ... -->
</div>
To quickly adjust the opacity of a color when referencing it as a variable in CSS, Tailwind includes a special --alpha() function:

CSS
@import "tailwindcss";
@layer components {
  .DocSearch-Hit--Result {
    background-color: --alpha(var(--color-gray-950) / 10%);
  }
}
Customizing your colors
Use @theme to add custom colors to your project under the --color-* theme namespace:

CSS
@import "tailwindcss";
@theme {
  --color-midnight: #121063;
  --color-tahiti: #3ab7bf;
  --color-bermuda: #78dcca;
}
Now utilities like bg-midnight, text-tahiti, and fill-bermuda will be available in your project in addition to the default colors.

Learn more about theme variables in the theme variables documentation.

Overriding default colors
Override any of the default colors by defining new theme variables with the same name:

CSS
@import "tailwindcss";
@theme {
  --color-gray-50: oklch(0.984 0.003 247.858);
  --color-gray-100: oklch(0.968 0.007 247.896);
  --color-gray-200: oklch(0.929 0.013 255.508);
  --color-gray-300: oklch(0.869 0.022 252.894);
  --color-gray-400: oklch(0.704 0.04 256.788);
  --color-gray-500: oklch(0.554 0.046 257.417);
  --color-gray-600: oklch(0.446 0.043 257.281);
  --color-gray-700: oklch(0.372 0.044 257.287);
  --color-gray-800: oklch(0.279 0.041 260.031);
  --color-gray-900: oklch(0.208 0.042 265.755);
  --color-gray-950: oklch(0.129 0.042 264.695);
}
Disabling default colors
Disable any default color by setting the theme namespace for that color to initial:

CSS
@import "tailwindcss";
@theme {
  --color-lime-*: initial;
  --color-fuchsia-*: initial;
}
This is especially useful for removing the corresponding CSS variables from your output for colors you don't intend to use.

Using a custom palette
Use --color-*: initial to completely disable all of the default colors and define your own custom color palette:

CSS
@import "tailwindcss";
@theme {
  --color-*: initial;
  --color-white: #fff;
  --color-purple: #3f3cbb;
  --color-midnight: #121063;
  --color-tahiti: #3ab7bf;
  --color-bermuda: #78dcca;
}
Referencing other variables
Use @theme inline when defining colors that reference other colors:

CSS
@import "tailwindcss";
:root {
  --acme-canvas-color: oklch(0.967 0.003 264.542);
}
[data-theme="dark"] {
  --acme-canvas-color: oklch(0.21 0.034 264.665);
}
@theme inline {
  --color-canvas: var(--acme-canvas-color);
}
Learn more in the theme documentation on referencing other variables.

Default color palette reference
Here's a complete list of the default colors and their values for reference:

CSS
@theme {
  --color-red-50: oklch(0.971 0.013 17.38);
  --color-red-100: oklch(0.936 0.032 17.717);
  --color-red-200: oklch(0.885 0.062 18.334);
  --color-red-300: oklch(0.808 0.114 19.571);
  --color-red-400: oklch(0.704 0.191 22.216);
  --color-red-500: oklch(0.637 0.237 25.331);
  --color-red-600: oklch(0.577 0.245 27.325);
  --color-red-700: oklch(0.505 0.213 27.518);
  --color-red-800: oklch(0.444 0.177 26.899);
  --color-red-900: oklch(0.396 0.141 25.723);
  --color-red-950: oklch(0.258 0.092 26.042);
  --color-orange-50: oklch(0.98 0.016 73.684);
  --color-orange-100: oklch(0.954 0.038 75.164);
  --color-orange-200: oklch(0.901 0.076 70.697);
  --color-orange-300: oklch(0.837 0.128 66.29);
  --color-orange-400: oklch(0.75 0.183 55.934);
  --color-orange-500: oklch(0.705 0.213 47.604);
  --color-orange-600: oklch(0.646 0.222 41.116);
  --color-orange-700: oklch(0.553 0.195 38.402);
  --color-orange-800: oklch(0.47 0.157 37.304);
  --color-orange-900: oklch(0.408 0.123 38.172);
  --color-orange-950: oklch(0.266 0.079 36.259);
  --color-amber-50: oklch(0.987 0.022 95.277);
  --color-amber-100: oklch(0.962 0.059 95.617);
  --color-amber-200: oklch(0.924 0.12 95.746);
  --color-amber-300: oklch(0.879 0.169 91.605);
  --color-amber-400: oklch(0.828 0.189 84.429);
  --color-amber-500: oklch(0.769 0.188 70.08);
  --color-amber-600: oklch(0.666 0.179 58.318);
  --color-amber-700: oklch(0.555 0.163 48.998);
  --color-amber-800: oklch(0.473 0.137 46.201);
  --color-amber-900: oklch(0.414 0.112 45.904);
  --color-amber-950: oklch(0.279 0.077 45.635);
  --color-yellow-50: oklch(0.987 0.026 102.212);
  --color-yellow-100: oklch(0.973 0.071 103.193);
  --color-yellow-200: oklch(0.945 0.129 101.54);
  --color-yellow-300: oklch(0.905 0.182 98.111);
  --color-yellow-400: oklch(0.852 0.199 91.936);
  --color-yellow-500: oklch(0.795 0.184 86.047);
  --color-yellow-600: oklch(0.681 0.162 75.834);
  --color-yellow-700: oklch(0.554 0.135 66.442);
  --color-yellow-800: oklch(0.476 0.114 61.907);
  --color-yellow-900: oklch(0.421 0.095 57.708);
  --color-yellow-950: oklch(0.286 0.066 53.813);
  --color-lime-50: oklch(0.986 0.031 120.757);
  --color-lime-100: oklch(0.967 0.067 122.328);
  --color-lime-200: oklch(0.938 0.127 124.321);
  --color-lime-300: oklch(0.897 0.196 126.665);
  --color-lime-400: oklch(0.841 0.238 128.85);
  --color-lime-500: oklch(0.768 0.233 130.85);
  --color-lime-600: oklch(0.648 0.2 131.684);
  --color-lime-700: oklch(0.532 0.157 131.589);
  --color-lime-800: oklch(0.453 0.124 130.933);
  --color-lime-900: oklch(0.405 0.101 131.063);
  --color-lime-950: oklch(0.274 0.072 132.109);
  --color-green-50: oklch(0.982 0.018 155.826);
  --color-green-100: oklch(0.962 0.044 156.743);
  --color-green-200: oklch(0.925 0.084 155.995);
  --color-green-300: oklch(0.871 0.15 154.449);
  --color-green-400: oklch(0.792 0.209 151.711);
  --color-green-500: oklch(0.723 0.219 149.579);
  --color-green-600: oklch(0.627 0.194 149.214);
  --color-green-700: oklch(0.527 0.154 150.069);
  --color-green-800: oklch(0.448 0.119 151.328);
  --color-green-900: oklch(0.393 0.095 152.535);
  --color-green-950: oklch(0.266 0.065 152.934);
  --color-emerald-50: oklch(0.979 0.021 166.113);
  --color-emerald-100: oklch(0.95 0.052 163.051);
  --color-emerald-200: oklch(0.905 0.093 164.15);
  --color-emerald-300: oklch(0.845 0.143 164.978);
  --color-emerald-400: oklch(0.765 0.177 163.223);
  --color-emerald-500: oklch(0.696 0.17 162.48);
  --color-emerald-600: oklch(0.596 0.145 163.225);
  --color-emerald-700: oklch(0.508 0.118 165.612);
  --color-emerald-800: oklch(0.432 0.095 166.913);
  --color-emerald-900: oklch(0.378 0.077 168.94);
  --color-emerald-950: oklch(0.262 0.051 172.552);
  --color-teal-50: oklch(0.984 0.014 180.72);
  --color-teal-100: oklch(0.953 0.051 180.801);
  --color-teal-200: oklch(0.91 0.096 180.426);
  --color-teal-300: oklch(0.855 0.138 181.071);
  --color-teal-400: oklch(0.777 0.152 181.912);
  --color-teal-500: oklch(0.704 0.14 182.503);
  --color-teal-600: oklch(0.6 0.118 184.704);
  --color-teal-700: oklch(0.511 0.096 186.391);
  --color-teal-800: oklch(0.437 0.078 188.216);
  --color-teal-900: oklch(0.386 0.063 188.416);
  --color-teal-950: oklch(0.277 0.046 192.524);
  --color-cyan-50: oklch(0.984 0.019 200.873);
  --color-cyan-100: oklch(0.956 0.045 203.388);
  --color-cyan-200: oklch(0.917 0.08 205.041);
  --color-cyan-300: oklch(0.865 0.127 207.078);
  --color-cyan-400: oklch(0.789 0.154 211.53);
  --color-cyan-500: oklch(0.715 0.143 215.221);
  --color-cyan-600: oklch(0.609 0.126 221.723);
  --color-cyan-700: oklch(0.52 0.105 223.128);
  --color-cyan-800: oklch(0.45 0.085 224.283);
  --color-cyan-900: oklch(0.398 0.07 227.392);
  --color-cyan-950: oklch(0.302 0.056 229.695);
  --color-sky-50: oklch(0.977 0.013 236.62);
  --color-sky-100: oklch(0.951 0.026 236.824);
  --color-sky-200: oklch(0.901 0.058 230.902);
  --color-sky-300: oklch(0.828 0.111 230.318);
  --color-sky-400: oklch(0.746 0.16 232.661);
  --color-sky-500: oklch(0.685 0.169 237.323);
  --color-sky-600: oklch(0.588 0.158 241.966);
  --color-sky-700: oklch(0.5 0.134 242.749);
  --color-sky-800: oklch(0.443 0.11 240.79);
  --color-sky-900: oklch(0.391 0.09 240.876);
  --color-sky-950: oklch(0.293 0.066 243.157);
  --color-blue-50: oklch(0.97 0.014 254.604);
  --color-blue-100: oklch(0.932 0.032 255.585);
  --color-blue-200: oklch(0.882 0.059 254.128);
  --color-blue-300: oklch(0.809 0.105 251.813);
  --color-blue-400: oklch(0.707 0.165 254.624);
  --color-blue-500: oklch(0.623 0.214 259.815);
  --color-blue-600: oklch(0.546 0.245 262.881);
  --color-blue-700: oklch(0.488 0.243 264.376);
  --color-blue-800: oklch(0.424 0.199 265.638);
  --color-blue-900: oklch(0.379 0.146 265.522);
  --color-blue-950: oklch(0.282 0.091 267.935);
  --color-indigo-50: oklch(0.962 0.018 272.314);
  --color-indigo-100: oklch(0.93 0.034 272.788);
  --color-indigo-200: oklch(0.87 0.065 274.039);
  --color-indigo-300: oklch(0.785 0.115 274.713);
  --color-indigo-400: oklch(0.673 0.182 276.935);
  --color-indigo-500: oklch(0.585 0.233 277.117);
  --color-indigo-600: oklch(0.511 0.262 276.966);
  --color-indigo-700: oklch(0.457 0.24 277.023);
  --color-indigo-800: oklch(0.398 0.195 277.366);
  --color-indigo-900: oklch(0.359 0.144 278.697);
  --color-indigo-950: oklch(0.257 0.09 281.288);
  --color-violet-50: oklch(0.969 0.016 293.756);
  --color-violet-100: oklch(0.943 0.029 294.588);
  --color-violet-200: oklch(0.894 0.057 293.283);
  --color-violet-300: oklch(0.811 0.111 293.571);
  --color-violet-400: oklch(0.702 0.183 293.541);
  --color-violet-500: oklch(0.606 0.25 292.717);
  --color-violet-600: oklch(0.541 0.281 293.009);
  --color-violet-700: oklch(0.491 0.27 292.581);
  --color-violet-800: oklch(0.432 0.232 292.759);
  --color-violet-900: oklch(0.38 0.189 293.745);
  --color-violet-950: oklch(0.283 0.141 291.089);
  --color-purple-50: oklch(0.977 0.014 308.299);
  --color-purple-100: oklch(0.946 0.033 307.174);
  --color-purple-200: oklch(0.902 0.063 306.703);
  --color-purple-300: oklch(0.827 0.119 306.383);
  --color-purple-400: oklch(0.714 0.203 305.504);
  --color-purple-500: oklch(0.627 0.265 303.9);
  --color-purple-600: oklch(0.558 0.288 302.321);
  --color-purple-700: oklch(0.496 0.265 301.924);
  --color-purple-800: oklch(0.438 0.218 303.724);
  --color-purple-900: oklch(0.381 0.176 304.987);
  --color-purple-950: oklch(0.291 0.149 302.717);
  --color-fuchsia-50: oklch(0.977 0.017 320.058);
  --color-fuchsia-100: oklch(0.952 0.037 318.852);
  --color-fuchsia-200: oklch(0.903 0.076 319.62);
  --color-fuchsia-300: oklch(0.833 0.145 321.434);
  --color-fuchsia-400: oklch(0.74 0.238 322.16);
  --color-fuchsia-500: oklch(0.667 0.295 322.15);
  --color-fuchsia-600: oklch(0.591 0.293 322.896);
  --color-fuchsia-700: oklch(0.518 0.253 323.949);
  --color-fuchsia-800: oklch(0.452 0.211 324.591);
  --color-fuchsia-900: oklch(0.401 0.17 325.612);
  --color-fuchsia-950: oklch(0.293 0.136 325.661);
  --color-pink-50: oklch(0.971 0.014 343.198);
  --color-pink-100: oklch(0.948 0.028 342.258);
  --color-pink-200: oklch(0.899 0.061 343.231);
  --color-pink-300: oklch(0.823 0.12 346.018);
  --color-pink-400: oklch(0.718 0.202 349.761);
  --color-pink-500: oklch(0.656 0.241 354.308);
  --color-pink-600: oklch(0.592 0.249 0.584);
  --color-pink-700: oklch(0.525 0.223 3.958);
  --color-pink-800: oklch(0.459 0.187 3.815);
  --color-pink-900: oklch(0.408 0.153 2.432);
  --color-pink-950: oklch(0.284 0.109 3.907);
  --color-rose-50: oklch(0.969 0.015 12.422);
  --color-rose-100: oklch(0.941 0.03 12.58);
  --color-rose-200: oklch(0.892 0.058 10.001);
  --color-rose-300: oklch(0.81 0.117 11.638);
  --color-rose-400: oklch(0.712 0.194 13.428);
  --color-rose-500: oklch(0.645 0.246 16.439);
  --color-rose-600: oklch(0.586 0.253 17.585);
  --color-rose-700: oklch(0.514 0.222 16.935);
  --color-rose-800: oklch(0.455 0.188 13.697);
  --color-rose-900: oklch(0.41 0.159 10.272);
  --color-rose-950: oklch(0.271 0.105 12.094);
  --color-slate-50: oklch(0.984 0.003 247.858);
  --color-slate-100: oklch(0.968 0.007 247.896);
  --color-slate-200: oklch(0.929 0.013 255.508);
  --color-slate-300: oklch(0.869 0.022 252.894);
  --color-slate-400: oklch(0.704 0.04 256.788);
  --color-slate-500: oklch(0.554 0.046 257.417);
  --color-slate-600: oklch(0.446 0.043 257.281);
  --color-slate-700: oklch(0.372 0.044 257.287);
  --color-slate-800: oklch(0.279 0.041 260.031);
  --color-slate-900: oklch(0.208 0.042 265.755);
  --color-slate-950: oklch(0.129 0.042 264.695);
  --color-gray-50: oklch(0.985 0.002 247.839);
  --color-gray-100: oklch(0.967 0.003 264.542);
  --color-gray-200: oklch(0.928 0.006 264.531);
  --color-gray-300: oklch(0.872 0.01 258.338);
  --color-gray-400: oklch(0.707 0.022 261.325);
  --color-gray-500: oklch(0.551 0.027 264.364);
  --color-gray-600: oklch(0.446 0.03 256.802);
  --color-gray-700: oklch(0.373 0.034 259.733);
  --color-gray-800: oklch(0.278 0.033 256.848);
  --color-gray-900: oklch(0.21 0.034 264.665);
  --color-gray-950: oklch(0.13 0.028 261.692);
  --color-zinc-50: oklch(0.985 0 0);
  --color-zinc-100: oklch(0.967 0.001 286.375);
  --color-zinc-200: oklch(0.92 0.004 286.32);
  --color-zinc-300: oklch(0.871 0.006 286.286);
  --color-zinc-400: oklch(0.705 0.015 286.067);
  --color-zinc-500: oklch(0.552 0.016 285.938);
  --color-zinc-600: oklch(0.442 0.017 285.786);
  --color-zinc-700: oklch(0.37 0.013 285.805);
  --color-zinc-800: oklch(0.274 0.006 286.033);
  --color-zinc-900: oklch(0.21 0.006 285.885);
  --color-zinc-950: oklch(0.141 0.005 285.823);
  --color-neutral-50: oklch(0.985 0 0);
  --color-neutral-100: oklch(0.97 0 0);
  --color-neutral-200: oklch(0.922 0 0);
  --color-neutral-300: oklch(0.87 0 0);
  --color-neutral-400: oklch(0.708 0 0);
  --color-neutral-500: oklch(0.556 0 0);
  --color-neutral-600: oklch(0.439 0 0);
  --color-neutral-700: oklch(0.371 0 0);
  --color-neutral-800: oklch(0.269 0 0);
  --color-neutral-900: oklch(0.205 0 0);
  --color-neutral-950: oklch(0.145 0 0);
  --color-stone-50: oklch(0.985 0.001 106.423);
  --color-stone-100: oklch(0.97 0.001 106.424);
  --color-stone-200: oklch(0.923 0.003 48.717);
  --color-stone-300: oklch(0.869 0.005 56.366);
  --color-stone-400: oklch(0.709 0.01 56.259);
  --color-stone-500: oklch(0.553 0.013 58.071);
  --color-stone-600: oklch(0.444 0.011 73.639);
  --color-stone-700: oklch(0.374 0.01 67.558);
  --color-stone-800: oklch(0.268 0.007 34.298);
  --color-stone-900: oklch(0.216 0.006 56.043);
  --color-stone-950: oklch(0.147 0.004 49.25);
  --color-black: #000;
  --color-white: #fff;
}
This can be useful if you want to reuse any of these scales but under a different name, like redefining --color-gray-* to use the --color-slate-* scale.

Core concepts

Adding custom styles
Best practices for adding your own custom styles in Tailwind projects.

Often the biggest challenge when working with a framework is figuring out what you’re supposed to do when there’s something you need that the framework doesn’t handle for you.

Tailwind has been designed from the ground up to be extensible and customizable, so that no matter what you’re building you never feel like you’re fighting the framework.

This guide covers topics like customizing your design tokens, how to break out of those constraints when necessary, adding your own custom CSS, and extending the framework with plugins.

Customizing your theme
If you want to change things like your color palette, spacing scale, typography scale, or breakpoints, add your customizations using the @theme directive in your CSS:

CSS
@theme {
  --font-display: "Satoshi", "sans-serif";
  --breakpoint-3xl: 120rem;
  --color-avocado-100: oklch(0.99 0 0);
  --color-avocado-200: oklch(0.98 0.04 113.22);
  --color-avocado-300: oklch(0.94 0.11 115.03);
  --color-avocado-400: oklch(0.92 0.19 114.08);
  --color-avocado-500: oklch(0.84 0.18 117.33);
  --color-avocado-600: oklch(0.53 0.12 118.34);
  --ease-fluid: cubic-bezier(0.3, 0, 0, 1);
  --ease-snappy: cubic-bezier(0.2, 0, 0, 1);
  /* ... */
}
Learn more about customizing your theme in the theme variables documentation.

Using arbitrary values
While you can usually build the bulk of a well-crafted design using a constrained set of design tokens, once in a while you need to break out of those constraints to get things pixel-perfect.

When you find yourself really needing something like top: 117px to get a background image in just the right spot, use Tailwind's square bracket notation to generate a class on the fly with any arbitrary value:

HTML
<div class="top-[117px]">
  <!-- ... -->
</div>
This is basically like inline styles, with the major benefit that you can combine it with interactive modifiers like hover and responsive modifiers like lg:

HTML
<div class="top-[117px] lg:top-[344px]">
  <!-- ... -->
</div>
This works for everything in the framework, including things like background colors, font sizes, pseudo-element content, and more:

HTML
<div class="bg-[#bada55] text-[22px] before:content-['Festivus']">
  <!-- ... -->
</div>
If you're referencing a CSS variable as an arbitrary value, you can use the custom property syntax:

HTML
<div class="fill-(--my-brand-color) ...">
  <!-- ... -->
</div>
This is just a shorthand for fill-[var(--my-brand-color)] that adds the var() function for you automatically.

Arbitrary properties
If you ever need to use a CSS property that Tailwind doesn't include a utility for out of the box, you can also use square bracket notation to write completely arbitrary CSS:

HTML
<div class="[mask-type:luminance]">
  <!-- ... -->
</div>
This is really like inline styles, but again with the benefit that you can use modifiers:

HTML
<div class="[mask-type:luminance] hover:[mask-type:alpha]">
  <!-- ... -->
</div>
This can be useful for things like CSS variables as well, especially when they need to change under different conditions:

HTML
<div class="[--scroll-offset:56px] lg:[--scroll-offset:44px]">
  <!-- ... -->
</div>
Arbitrary variants
Arbitrary variants are like arbitrary values but for doing on-the-fly selector modification, like you can with built-in pseudo-class variants like hover:{utility} or responsive variants like md:{utility} but using square bracket notation directly in your HTML.

HTML
<ul role="list">
  {#each items as item}
  <li class="lg:[&:nth-child(-n+3)]:hover:underline">{item}</li>
  {/each}
</ul>
Learn more in the arbitrary variants documentation.

Handling whitespace
When an arbitrary value needs to contain a space, use an underscore (_) instead and Tailwind will automatically convert it to a space at build-time:

HTML
<div class="grid grid-cols-[1fr_500px_2fr]">
  <!-- ... -->
</div>
In situations where underscores are common but spaces are invalid, Tailwind will preserve the underscore instead of converting it to a space, for example in URLs:

HTML
<div class="bg-[url('/what_a_rush.png')]">
  <!-- ... -->
</div>
In the rare case that you actually need to use an underscore but it's ambiguous because a space is valid as well, escape the underscore with a backslash and Tailwind won't convert it to a space:

HTML
<div class="before:content-['hello\_world']">
  <!-- ... -->
</div>
If you're using something like JSX where the backslash is stripped from the rendered HTML, use String.raw() so the backslash isn't treated as a JavaScript escape character:

<div className={String.raw`before:content-['hello\_world']`}>
  <!-- ... -->
</div>
Resolving ambiguities
Many utilities in Tailwind share a common namespace but map to different CSS properties. For example text-lg and text-black both share the text- namespace, but one is for font-size and the other is for color.

When using arbitrary values, Tailwind can generally handle this ambiguity automatically based on the value you pass in:

HTML
<!-- Will generate a font-size utility -->
<div class="text-[22px]">...</div>
<!-- Will generate a color utility -->
<div class="text-[#bada55]">...</div>
Sometimes it really is ambiguous though, for example when using CSS variables:

HTML
<div class="text-(--my-var)">...</div>
In these situations, you can "hint" the underlying type to Tailwind by adding a CSS data type before the value:

HTML
<!-- Will generate a font-size utility -->
<div class="text-(length:--my-var)">...</div>
<!-- Will generate a color utility -->
<div class="text-(color:--my-var)">...</div>
Using custom CSS
While Tailwind is designed to handle the bulk of your styling needs, there is nothing stopping you from just writing plain CSS when you need to:

CSS
@import "tailwindcss";
.my-custom-style {
  /* ... */
}
Adding base styles
If you just want to set some defaults for the page (like the text color, background color, or font family), the easiest option is just adding some classes to the html or body elements:

HTML
<!doctype html>
<html lang="en" class="bg-gray-100 font-serif text-gray-900">
  <!-- ... -->
</html>
This keeps your base styling decisions in your markup alongside all of your other styles, instead of hiding them in a separate file.

If you want to add your own default base styles for specific HTML elements, use the @layer directive to add those styles to Tailwind's base layer:

CSS
@layer base {
  h1 {
    font-size: var(--text-2xl);
  }
  h2 {
    font-size: var(--text-xl);
  }
}
Adding component classes
Use the components layer for any more complicated classes you want to add to your project that you'd still like to be able to override with utility classes.

Traditionally these would be classes like card, btn, badge — that kind of thing.

CSS
@layer components {
  .card {
    background-color: var(--color-white);
    border-radius: var(--radius-lg);
    padding: --spacing(6);
    box-shadow: var(--shadow-xl);
  }
}
By defining component classes in the components layer, you can still use utility classes to override them when necessary:

HTML
<!-- Will look like a card, but with square corners -->
<div class="card rounded-none">
  <!-- ... -->
</div>
Using Tailwind you probably don't need these types of classes as often as you think. Read our guide on managing duplication for our recommendations.

The components layer is also a good place to put custom styles for any third-party components you're using:

CSS
@layer components {
  .select2-dropdown {
    /* ... */
  }
}
Using variants
Use the @variant directive to apply a Tailwind variant within custom CSS:

app.css
.my-element {
  background: white;
  @variant dark {
    background: black;
  }
}
Compiled CSS
.my-element {
  background: white;
  @media (prefers-color-scheme: dark) {
    background: black;
  }
}
If you need to apply multiple variants at the same time, use nesting:

app.css
.my-element {
  background: white;
  @variant dark {
    @variant hover {
      background: black;
    }
  }
}
Compiled CSS
.my-element {
  background: white;
  @media (prefers-color-scheme: dark) {
    &:hover {
      @media (hover: hover) {
        background: black;
      }
    }
  }
}
Adding custom utilities
Simple utilities
In addition to using the utilities that ship with Tailwind, you can also add your own custom utilities. This can be useful when there's a CSS feature you'd like to use in your project that Tailwind doesn't include utilities for out of the box.

Use the @utility directive to add a custom utility to your project:

CSS
@utility content-auto {
  content-visibility: auto;
}
You can now use this utility in your HTML:

HTML
<div class="content-auto">
  <!-- ... -->
</div>
It will also work with variants like hover, focus and lg:

HTML
<div class="hover:content-auto">
  <!-- ... -->
</div>
Custom utilities are automatically inserted into the utilities layer along with all of the built-in utilities in the framework.

Complex utilities
If your custom utility is more complex than a single class name, use nesting to define the utility:

CSS
@utility scrollbar-hidden {
  &::-webkit-scrollbar {
    display: none;
  }
}
Functional utilities
In addition to registering simple utilities with the @utility directive, you can also register functional utilities that accept an argument:

CSS
@utility tab-* {
  tab-size: --value(--tab-size-*);
}
The special --value() function is used to resolve the utility value.

Matching theme values
Use the --value(--theme-key-*) syntax to resolve the utility value against a set of theme keys:

CSS
@theme {
  --tab-size-2: 2;
  --tab-size-4: 4;
  --tab-size-github: 8;
}
@utility tab-* {
  tab-size: --value(--tab-size-*);
}
This will match utilities like tab-2, tab-4, and tab-github.

Bare values
To resolve the value as a bare value, use the --value({type}) syntax, where {type} is the data type you want to validate the bare value as:

CSS
@utility tab-* {
  tab-size: --value(integer);
}
This will match utilities like tab-1 and tab-76.

Literal values
To support literal values, use the --value('literal') syntax (notice the quotes):

CSS
@utility tab-* {
  tab-size: --value("inherit", "initial", "unset");
}
This will match utilities like tab-inherit, tab-initial, and tab-unset.

Arbitrary values
To support arbitrary values, use the --value([{type}]) syntax (notice the square brackets) to tell Tailwind which types are supported as an arbitrary value:

CSS
@utility tab-* {
  tab-size: --value([integer]);
}
This will match utilities like tab-[1] and tab-[76]. If you want to support any data type, you can use --value([*]).

Supporting theme, bare, and arbitrary values together
All three forms of the --value() function can be used within a rule as multiple declarations, and any declarations that fail to resolve will be omitted in the output:

CSS
@theme {
  --tab-size-github: 8;
}
@utility tab-* {
  tab-size: --value([integer]);
  tab-size: --value(integer);
  tab-size: --value(--tab-size-*);
}
This makes it possible to treat the value differently in each case if necessary, for example translating a bare integer to a percentage:

CSS
@utility opacity-* {
  opacity: --value([percentage]);
  opacity: calc(--value(integer) * 1%);
  opacity: --value(--opacity-*);
}
The --value() function can also take multiple arguments and resolve them left to right if you don't need to treat the return value differently in different cases:

CSS
@theme {
  --tab-size-github: 8;
}
@utility tab-* {
  tab-size: --value(--tab-size-*, integer, [integer]);
}
@utility opacity-* {
  opacity: calc(--value(integer) * 1%);
  opacity: --value(--opacity-*, [percentage]);
}
Negative values
To support negative values, register separate positive and negative utilities into separate declarations:

CSS
@utility inset-* {
  inset: --spacing(--value(integer));
  inset: --value([percentage], [length]);
}
@utility -inset-* {
  inset: --spacing(--value(integer) * -1);
  inset: calc(--value([percentage], [length]) * -1);
}
Modifiers
Modifiers are handled using the --modifier() function which works exactly like the --value() function but operates on a modifier if present:

CSS
@utility text-* {
  font-size: --value(--text-*, [length]);
  line-height: --modifier(--leading-*, [length], [*]);
}
If a modifier isn't present, any declaration depending on a modifier is just not included in the output.

Fractions
To handle fractions, we rely on the CSS ratio data type. If this is used with --value(), it's a signal to Tailwind to treat the value and modifier as a single value:

CSS
@utility aspect-* {
  aspect-ratio: --value(--aspect-ratio-*, ratio, [ratio]);
}
This will match utilities like aspect-square, aspect-3/4, and aspect-[7/9].

Adding custom variants
In addition to using the variants that ship with Tailwind, you can also add your own custom variants using the @custom-variant directive:

@custom-variant theme-midnight {
  &:where([data-theme="midnight"] *) {
    @slot;
  }
}
Now you can use the theme-midnight:<utility> variant in your HTML:

<html data-theme="midnight">
  <button class="theme-midnight:bg-black ..."></button>
</html>
You can create variants using the shorthand syntax when nesting isn't required:

@custom-variant theme-midnight (&:where([data-theme="midnight"] *));
When a custom variant has multiple rules, they can be nested within each other:

@custom-variant any-hover {
  @media (any-hover: hover) {
    &:hover {
      @slot;
    }
  }
}

Detecting classes in source files
Understanding and customizing how Tailwind scans your source files.

Overview
Tailwind works by scanning your project for utility classes, then generating all of the necessary CSS based on the classes you've actually used.

This makes sure your CSS is as small as possible, and is also what makes features like arbitrary values possible.

How classes are detected
Tailwind treats all of your source files as plain text, and doesn't attempt to actually parse your files as code in any way.

Instead it just looks for any tokens in your file that could be classes based on which characters Tailwind is expecting in class names:

JSX
export function Button({ color, children }) {
  const colors = {
    black: "bg-black text-white",
    blue: "bg-blue-500 text-white",
    white: "bg-white text-black",
  };
  return (
    <button className={`${colors[color]} rounded-full px-2 py-1.5 font-sans text-sm/6 font-medium shadow`}>
      {children}
    </button>
  );
}
Then it tries to generate the CSS for all of these tokens, throwing away any tokens that don't map to a utility class the framework knows about.

Dynamic class names
Since Tailwind scans your source files as plain text, it has no way of understanding string concatenation or interpolation in the programming language you're using.

Don't construct class names dynamically

HTML
<div class="text-{{ error ? 'red' : 'green' }}-600"></div>
In the example above, the strings text-red-600 and text-green-600 do not exist, so Tailwind will not generate those classes.

Instead, make sure any class names you’re using exist in full:

Always use complete class names

HTML
<div class="{{ error ? 'text-red-600' : 'text-green-600' }}"></div>
If you're using a component library like React or Vue, this means you shouldn't use props to dynamically construct classes:

Don't use props to build class names dynamically

JSX
function Button({ color, children }) {
  return <button className={`bg-${color}-600 hover:bg-${color}-500 ...`}>{children}</button>;
}
Instead, map props to complete class names that are statically detectable at build-time:

Always map props to static class names

JSX
function Button({ color, children }) {
  const colorVariants = {
    blue: "bg-blue-600 hover:bg-blue-500",
    red: "bg-red-600 hover:bg-red-500",
  };
  return <button className={`${colorVariants[color]} ...`}>{children}</button>;
}
This has the added benefit of letting you map different prop values to different color shades for example:

JSX
function Button({ color, children }) {
  const colorVariants = {
    blue: "bg-blue-600 hover:bg-blue-500 text-white",
    red: "bg-red-500 hover:bg-red-400 text-white",
    yellow: "bg-yellow-300 hover:bg-yellow-400 text-black",
  };
  return <button className={`${colorVariants[color]} ...`}>{children}</button>;
}
As long as you always use complete class names in your code, Tailwind will generate all of your CSS perfectly every time.

Which files are scanned
Tailwind will scan every file in your project for class names, except in the following cases:

Files that are in your .gitignore file
Files in the node_modules directory
Binary files like images, videos, or zip files
CSS files
Common package manager lock files
If you need to scan any files that Tailwind is ignoring by default, you can explicitly register those sources.

Explicitly registering sources
Use @source to explicitly register source paths relative to the stylesheet:

CSS
@import "tailwindcss";
@source "../node_modules/@acmecorp/ui-lib";
This is especially useful when you need to scan an external library that is built with Tailwind, since dependencies are usually listed in your .gitignore file and ignored by Tailwind by default.

Setting your base path
Tailwind uses the current working directory as its starting point when scanning for class names by default.

To set the base path for source detection explicitly, use the source() function when importing Tailwind in your CSS:

CSS
@import "tailwindcss" source("../src");
This can be useful when working with monorepos where your build commands run from the root of the monorepo instead of the root of each project.

Ignoring specific paths
Use @source not to ignore specific paths, relative to the stylesheet, when scanning for class names:

CSS
@import "tailwindcss";
@source not "../src/components/legacy";
This is useful when you have large directories in your project that you know don't use Tailwind classes, like legacy components or third-party libraries.

Disabling automatic detection
Use source(none) to completely disable automatic source detection if you want to register all of your sources explicitly:

CSS
@import "tailwindcss" source(none);
@source "../admin";
@source "../shared";
This can be useful in projects that have multiple Tailwind stylesheets where you want to make sure each one only includes the classes each stylesheet needs.

Safelisting specific utilities
If you need to make sure Tailwind generates certain class names that don’t exist in your content files, use @source inline() to force them to be generated:

CSS
@import "tailwindcss";
@source inline("underline");
Generated CSS
.underline {
  text-decoration-line: underline;
}
Safelisting variants
You can also use @source inline() to generate classes with variants. For example, to generate the underline class with hover and focus variants, add {hover:,focus:,} to the source input:

CSS
@import "tailwindcss";
@source inline("{hover:,focus:,}underline");
Generated CSS
.underline {
  text-decoration-line: underline;
}
@media (hover: hover) {
  .hover\:underline:hover {
    text-decoration-line: underline;
  }
}
@media (focus: focus) {
  .focus\:underline:focus {
    text-decoration-line: underline;
  }
}
Safelisting with ranges
The source input is brace expanded, so you can generate multiple classes at once. For example, to generate all the red background colors with hover variants, use a range:

CSS
@import "tailwindcss";
@source inline("{hover:,}bg-red-{50,{100..900..100},950}");
Generated CSS
.bg-red-50 {
  background-color: var(--color-red-50);
}
.bg-red-100 {
  background-color: var(--color-red-100);
}
.bg-red-200 {
  background-color: var(--color-red-200);
}
/* ... */
.bg-red-800 {
  background-color: var(--color-red-800);
}
.bg-red-900 {
  background-color: var(--color-red-900);
}
.bg-red-950 {
  background-color: var(--color-red-950);
}
@media (hover: hover) {
  .hover\:bg-red-50:hover {
    background-color: var(--color-red-50);
  }
  /* ... */
  .hover\:bg-red-950:hover {
    background-color: var(--color-red-950);
  }
}
This generates red background colors from 100 to 900 in increments of 100, along with the first and last shades of 50 and 950. It also adds the hover: variant for each of those classes.

Explicitly excluding classes
Use @source not inline() to prevent specific classes from being generated, even if they are detected in your source files:

CSS
@import "tailwindcss";
@source not inline("{hover:,focus:,}bg-red-{50,{100..900..100},950}");
This will explicitly exclude the red background utilities, along with their hover and focus variants, from being generated.

Functions and directives
A reference for the custom functions and directives Tailwind exposes to your CSS.

Directives
Directives are custom Tailwind-specific at-rules you can use in your CSS that offer special functionality for Tailwind CSS projects.

@import
Use the @import directive to inline import CSS files, including Tailwind itself:

CSS
@import "tailwindcss";
@theme
Use the @theme directive to define your project's custom design tokens, like fonts, colors, and breakpoints:

CSS
@theme {
  --font-display: "Satoshi", "sans-serif";
  --breakpoint-3xl: 120rem;
  --color-avocado-100: oklch(0.99 0 0);
  --color-avocado-200: oklch(0.98 0.04 113.22);
  --color-avocado-300: oklch(0.94 0.11 115.03);
  --color-avocado-400: oklch(0.92 0.19 114.08);
  --color-avocado-500: oklch(0.84 0.18 117.33);
  --color-avocado-600: oklch(0.53 0.12 118.34);
  --ease-fluid: cubic-bezier(0.3, 0, 0, 1);
  --ease-snappy: cubic-bezier(0.2, 0, 0, 1);
  /* ... */
}
Learn more about customizing your theme in the theme variables documentation.

@source
Use the @source directive to explicitly specify source files that aren't picked up by Tailwind's automatic content detection:

CSS
@source "../node_modules/@my-company/ui-lib";
Learn more about automatic content detection in the detecting classes in source files documentation.

@utility
Use the @utility directive to add custom utilities to your project that work with variants like hover, focus and lg:

CSS
@utility tab-4 {
  tab-size: 4;
}
Learn more about registering custom utilities in the adding custom utilities documentation.

@variant
Use the @variant directive to apply a Tailwind variant to styles in your CSS:

CSS
.my-element {
  background: white;
  @variant dark {
    background: black;
  }
}
Learn more using variants in the using variants documentation.

@custom-variant
Use the @custom-variant directive to add a custom variant in your project:

CSS
@custom-variant theme-midnight (&:where([data-theme="midnight"] *));
This lets you write utilities theme-midnight:bg-black and theme-midnight:text-white.

Learn more about adding custom variants in the adding custom variants documentation.

@apply
Use the @apply directive to inline any existing utility classes into your own custom CSS:

CSS
.select2-dropdown {
  @apply rounded-b-lg shadow-md;
}
.select2-search {
  @apply rounded border border-gray-300;
}
.select2-results__group {
  @apply text-lg font-bold text-gray-900;
}
This is useful when you need to write custom CSS (like to override the styles in a third-party library) but still want to work with your design tokens and use the same syntax you’re used to using in your HTML.

@reference
If you want to use @apply or @variant in the <style> block of a Vue or Svelte component, or within CSS modules, you will need to import your theme variables, custom utilities, and custom variants to make those values available in that context.

To do this without duplicating any CSS in your output, use the @reference directive to import your main stylesheet for reference without actually including the styles:

Vue
<template>
  <h1>Hello world!</h1>
</template>
<style>
  @reference "../../app.css";
  h1 {
    @apply text-2xl font-bold text-red-500;
  }
</style>
If you’re just using the default theme with no customizations, you can import tailwindcss directly:

Vue
<template>
  <h1>Hello world!</h1>
</template>
<style>
  @reference "tailwindcss";
  h1 {
    @apply text-2xl font-bold text-red-500;
  }
</style>
Functions
Tailwind provides the following build-time functions to make working with colors and the spacing scale easier.

--alpha()
Use the --alpha() function to adjust the opacity of a color:

Input CSS
.my-element {
  color: --alpha(var(--color-lime-300) / 50%);
}
Compiled CSS
.my-element {
  color: color-mix(in oklab, var(--color-lime-300) 50%, transparent);
}
--spacing()
Use the --spacing() function to generate a spacing value based on your theme:

Input CSS
.my-element {
  margin: --spacing(4);
}
Compiled CSS
.my-element {
  margin: calc(var(--spacing) * 4);
}
This can also be useful in arbitrary values, especially in combination with calc():

HTML
<div class="py-[calc(--spacing(4)-1px)]">
  <!-- ... -->
</div>
Compatibility
The following directives and functions exist solely for compatibility with Tailwind CSS v3.x.

The @config and @plugin directives may be used in conjunction with @theme, @utility, and other CSS-driven features. This can be used to incrementally move over your theme, custom configuration, utilities, variants, and presets to CSS. Things defined in CSS will be merged where possible and otherwise take precedence over those defined in configs, presets, and plugins.

@config
Use the @config directive to load a legacy JavaScript-based configuration file:

CSS
@config "../../tailwind.config.js";
The corePlugins, safelist, and separator options from the JavaScript-based config are not supported in v4.0. To safelist utilities in v4 use @source inline().

@plugin
Use the @plugin directive to load a legacy JavaScript-based plugin:

CSS
@plugin "@tailwindcss/typography";
The @plugin directive accepts either a package name or a local path.

theme()
Use the theme() function to access your Tailwind theme values using dot notation:

CSS
.my-element {
  margin: theme(spacing.12);
}
This function is deprecated, and we recommend using CSS theme variables instead.
