# Troubleshooting

## My repository is detected as the wrong language

If the language stats bar is reporting a language that you don't expect:

1. Click on the name of the language in the stats bar to see a list of the files that are identified as that language.
   Keep in mind this performs a search so the [code search restrictions][search-limits] may result in files identified in the language statistics not appearing in the search results.
   [Installing Linguist locally](/README.md/#installation) and running it from the [command line](/README.md#command-line-usage) will give you accurate results.
2. If you see files that you didn't write in the search results, consider moving the files into one of the [paths for vendored code](/lib/linguist/vendor.yml), or use the [manual overrides](/docs/overrides.md) feature to ignore them.
3. If the files are misclassified, search for [open issues](https://github.com/github/linguist/issues) to see if anyone else has already reported the issue.
   Any information you can add, especially links to public repositories, is helpful.
   You can also use the [manual overrides](/docs/overrides.md) feature to correctly classify them in your repository.
4. If there are no reported issues of this misclassification, [open an issue](https://github.com/github/linguist/issues/new) and include a link to the repository or a sample of the code that is being misclassified.

[search-limits]: https://docs.github.com/github/searching-for-information-on-github/searching-code#considerations-for-code-search

Keep in mind that the repository language stats are only [updated when you push changes](how-linguist-works.md#how-linguist-works-on-githubcom), and the results are cached for the lifetime of your repository.
If you have not made any changes to your repository in a while, you may find pushing another change will correct the stats.

## When I click on a language present in the language stats bar, I get a message saying "Your search did not match any code"

There are a few reasons that could lead to this outcome:

1. If the repository implements an override via the `linguist-language` attribute, it won't be taken into account in GitHub's search results since GitHub search relies on an internal library which doesn't support overrides at the moment.
2. GitHub's search internal library might not be using the latest version of Linguist which means that files could be detected differently in search compared to Linguist (see also the note at the end of [this section](#my-linguist-pr-has-been-merged-but-github-doesnt-reflect-my-changes)).
3. It could be that files are associated to a language that is part of a group. This means that they are counted as the parent language in the language stats bar, but as the actual language in search. For instance, a file ending with `.f90` is considered to be  "Fortran" in the stats bar, but "Fortran Free Form" in search.
4. Finally, this can be caused by [code search limitations](https://docs.github.com/en/search-github/github-code-search/about-github-code-search#limitations) that are unrelated to Linguist.

## My C/C++/Objective-C `.h` header file is detected as the wrong language

Correctly detecting the language for the C-family `.h` header files is tough because Linguist detects the languages of files in isolation when analysing repositories and these header files, especially the smaller ones, can be used across all three languages without using any language-specific content.
To try and reduce the number of false positives and keep some degree of predictability for users, Linguist will assume all `.h` header files are C by default and will only identify a file as C++ or Objective-C if the content matches the specific heuristic for that language.
This will mean you will need to implement an [override](/docs/overrides.md) for some of your header files if you wish for them to be classified as C++ or Objective-C if they do not contain language-specific content.

## My repository isn't showing my language

Linguist does not consider [vendored code](/docs/overrides.md#vendored-code), [generated code](/docs/overrides.md#generated-code), [documentation](/docs/overrides.md#documentation), or `data` (e.g. SQL) or `prose` (e.g. Markdown) languages (as defined by the `type` attribute in [`languages.yml`](/lib/linguist/languages.yml)) when calculating the repository language statistics.

If the language statistics bar is not showing your language at all, it could be for a few reasons:

1. Linguist doesn't know about your language.
2. The extension you have chosen is not associated with your language in [`languages.yml`](/lib/linguist/languages.yml).
3. All the files in your repository fall into one of the categories listed above that Linguist excludes by default.

If Linguist doesn't know about the language or the extension you're using, consider [contributing](/CONTRIBUTING.md) to Linguist by opening a pull request to add support for your language or extension.
For everything else, you can use the [manual overrides](/docs/overrides.md) feature to tell Linguist to include your files in the language statistics.

## There's a problem with the syntax highlighting of a file

Linguist detects the language of a file but the actual syntax-highlighting is powered by a set of language grammars which are included in this project as a set of submodules [as listed here](/vendor/README.md).

If you experience an issue with the syntax-highlighting on GitHub, **please report the issue to the upstream grammar repository, not here.**
Grammars are updated every time we build the Linguist gem so upstream bug fixes are automatically incorporated as they are fixed.

## I get an error when using Linguist on a directory that is not a Git repository

Linguist only works on Git repositories and individual files. Its primary use is on GitHub.com which uses bare
repositories and thus changes need to be committed as individual files don't show on the filesystem.

As a work around you could initialise a temporary Git repository in your directory as demonstrated in this
[script](https://gist.github.com/PuZZleDucK/a45fd1fac3758235ffed9fe0e8aab643).

Alternatively you can run Linguist on individual files, see [here](/README.md#single-file).

## I am unable to install Linguist on macOS

There are several known issues with the version of Ruby shipped with macOS that cause problems when it comes to installing the charlock-holmes gem which is a Linguist dependency.
As this is a problem with the Ruby shipped by Apple and not Linguist or charlock-holmes, we recommend you install a version of Ruby using Homebrew, `rbenv`, `rvm`, `ruby-build`, `asdf` or other packaging system, before attempting to install Linguist.

## My Linguist PR has been merged but GitHub doesn't reflect my changes

Changes to Linguist will only appear on GitHub when a new Linguist release is made and this is deployed to GitHub.com.
There is no set release timeframe, but the intention is at least once every three-to-four months so the latest version ships with each new major version of GitHub Enterprise Server.
A PR like [this](https://github.com/github/linguist/pull/6051) will be created for the release with checkmarks for each stage of the release process.

All syntax highlighting grammars will also be updated in all major and minor releases.
Grammars will only be updated in patch releases if the patch release is specifically for that language and it requires a grammar update to address the issue.

Note: New languages will not appear in GitHub's search results for some time after the pull request has been merged and the new Linguist release deployed to GitHub.com.
This is because GitHub's search uses a separate internal library for language detection which tends to lag behind Linguist by a few weeks to months.
