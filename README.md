# Lessonly Coding Style Guide

Powered by [Jekyll](https://github.com/jekyll/jekyll) and hosted by [Github Pages](https://pages.github.com/).

## Running it Locally

    $ git clone git@github.com:lessonly/lessonly.github.io.git
    $ cd lessonly.github.io
    $ bundle install
    $ bundle exec jekyll serve

See your changes reflected at <http://localhost:4000>

## Contributing

1. Create a branch.
2. Commit your changes.
3. Push the branch to Github.
4. Open a pull request for discussion.
5. Merge and Profit!

## Links

When using links be sure to use the liquid variable {{ site.github.url }} since we host the site on Github pages. [Why?](https://jekyllrb.com/docs/github-pages/#project-page-url-structure)

### Styleguides

When adding new content to our styleguide first create a new file within the _styleguide directory. The title of the page from the front-matter (e.g. "Ruby Style Guide") becomes the link text on the http://lessonly.github.io/styleguide listing.

Things to consider when adding content:
* Use examples! Examples help the rest of the team understand concepts and more clearly implement them.
* Sometimes the justification or link to the reasoning behind a standard is helpful to know. These help illustrate why we made the decision we did.

### Blog Posts

It's always good to get a second opinion when writing a new post to make sure it is relevant. Consider asking the team in chat if they think your new post topic on, "My Ruby Kittens gem. Awww cute" is something we can inspire other developers to read.

1. jekyll post "My new draft name"
2. Write your post
3. When you are finished push your changes up to a new PR
4. Let the team know the post is ready for content editing

