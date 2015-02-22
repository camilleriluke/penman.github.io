---
layout: default
title: How I test Sass
---

**tl;dr: My tiny Sass unit testing library is simple to use and understand, can enforce your minimum supported Sass version, encourages separation of concerns, and is easy to integrate with continuous integration. You can find it [on GitHub](https://github.com/penman/SassUnit).**

I maintain [Sass-Web-Fonts](https://github.com/penman/Sass-Web-Fonts), a fairly complex Sass mixin for using Google Web Fonts. Every time I tried to work on it, though, it was a huge pain to ensure I hadn’t broken existing functionality with new changes. I ended up doing it in [CodePen](https://codepen.io), writing a load of examples I hoped would cover the entire functionality of the mixin, pasting in the contents of the mixin at the top, and checking the compiled output. This made working on the mixin time-consuming and intimidating, so I looked for a better solution.

I was inspired by The Guardian’s [sass-mq](https://github.com/guardian/sass-mq) mixin, which I felt was similar in scope to mine. Their approach to testing is to have a “test” folder with “test.scss” and “test.css” files. After making a change to the mixin, the developer compiles “test.scss”, overriding “test.css”, and then runs `git diff` and makes sure the output hasn’t changed.

While I like the idea of having test Sass in one file, with the expected output in a corresponding CSS file, The Guardian’s method felt like it could be simplified, and the single test file could end up becoming a monolith on a large project.

My solution is to split each small test case into its own file, (usually no more than 4-5 lines), each with a corresponding CSS file containing the desired output. A tiny Ruby script iterates through each example and displays the result in an RSpec-style format like this using Ruby’s built-in Minitest library:

![The output of SassUnit is like RSpec, with each passing test displayed as a dot.](/img/posts/testing-sass/output.png)

This was simple, but very effective, and provides several extra benefits that I didn’t anticipate. Firstly, running the script through `bundle exec` can ensure that the tests are run with your minimum supported Sass version, if you specify it in a Gemfile for your project. This will prevent you inadvertently using a feature only available in newer versions of Sass, which you may not otherwise notice if you’re not always running the older version. Additionally, my approach is much easier to use [continuous integration](https://en.wikipedia.org/wiki/Continuous_integration). Since it uses piggybacks a standard unit testing library, all I had to do to set up CI - I use [Codeship](https://codeship.com) - was paste in the command I would use to run the tests normally. Good luck getting CI to understand that passing the tests means no diff in a specific file between commits - you’d end up writing more code than I did for my tiny library!

**My Sass testing library is called SassUnit. You can install it with `gem install sassunit`, and [find documentation or contribute on Github](https://github.com/penman/SassUnit).**

[The initial version was just 16 lines of code](https://github.com/penman/Sass-Web-Fonts/blob/28637b6502169d8ec0895d0e801a8894ae1151b0/test.rb), and is already super useful. There are definitely things I could add though, like allowing tests to be run under both Ruby Sass and [libsass](http://libsass.org). If there’s anything else you feel could be added, feel free to [open an issue on GitHub](https://github.com/penman/SassUnit/issues/new).
