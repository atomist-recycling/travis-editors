# Atomist 'travis-editors'

[![Build Status](https://travis-ci.org/atomist-rugs/travis-editors.svg?branch=master)](https://travis-ci.org/atomist-rugs/travis-editors)
[![Slack Status](https://join.atomist.com/badge.svg)](https://join.atomist.com)

This [Rug](http://docs.atomist.com/) archive has editors that enable
and configure a [Travis CI](https://travis-ci.org/) build for a GitHub
repository.  These Rugs work with both public (travis-ci.org) and
private (travis-ci.com) builds.

This project uses an extended version of the Rug language, using the [Travis language extension](https://github.com/atomist-rugs/travis-rug-type).

## Rugs

### EnableTravisForRugArchiveDSL and EnableTravisForRugArchiveTS

The EnableTravisForRugArchiveDSL and EnableTravisForRugArchiveTS are
implementations of the exact same functionality using two different
Rug languages: the [Rug DSL][rug] and [TypeScript][ts].

[rug]: http://docs.atomist.com/
[ts]: https://www.typescriptlang.org/

#### Prerequisites

Before running this Editor, you must have the following prerequisites
satisfied.

*   A GitHub repository for a Rug archive cloned locally.
*   Travis CI authorized to access the repository containing your
    Rugs.  Typically, Travis CI authorizations are done on an
    user/organization basis.  See the Travis
    CI [Getting Started Guide][travis-start] for more information.

#### Parameters

To run this editor, you must supply the following parameters.

*   `repo_slug`: The Rug archive's GitHub repository slug, e.g.,
    `atomist-rugs/common-editors`.
*   `github_token`: A [GitHub Personal Access Token][gh-token] with
    the following scopes:

    -    `repo`: for Travis CI, creating tags
    -    `write:repo_hook`: for Travis CI
    -    `user:email`: for Travis CI
    -    `read:org`: for Travis CI

    This token is used by Travis CI to authenticate to GitHub for
    these edits.  It is not needed and can be deleted after the Editor
    completes.  These above scopes are a union of the permissions
    required by Travis CI to for both public and private GitHub
    repositories.  The token must be a from a GitHub user who is an
    owner of the repository.  If the owner of the repository is a
    GitHub organization, this means the token must be from a user in
    the Owner group.  Sometimes the scopes required by Travis CI
    change.  You can get the current list of scopes directly from
    Travis CI.  Change `ENDPOINT` to `com` if running the Editor on
    private repositories or `org` if running the Editor on public
    repositories.  If you want to use the same token for both, run the
    command below against both endpoints and create a token with a
    union of the scopes.

        $ curl -s -H 'Content-Type: application/json' -H 'User-Agent: CurlClient/1.0.0' -H 'Accept: application/vnd.travis-ci.2+json' https://api.travis-ci.ENDPOINT/config | jq .config.github.scopes

*   `org`: Select which Travis CI endpoint to use.  Set to `.org` if
    `repo_slug` is a public repository and `.com` if it is a private
    repository.
*   `maven_base_url`, `maven_user`, `maven_token`: A [Maven][maven]
    repository base URL and user name & password/token where the Rug
    archive will be published.  The base URL is the Maven repository
    URL without the trailing repository name.  The Maven repositories
    `maven_base_url`/rugs-dev and `maven_base_url`/rugs-release must
    exist and `maven_user` must have write access to them.  It is
    recommended to create a dedicated Maven CI user with just this
    access.

[travis-start]: https://docs.travis-ci.com/user/getting-started/
[gh-token]: https://github.com/settings/tokens
[maven]: https://maven.apache.org/

#### Running

Run it as follows:

```
$ cd to/your/rug/archive/repo
$ rug edit atomist-rugs:travis-editors:EnableTravisForRugArchiveDSL \
    repo_slug=$REPO_OWNER/$REPO_NAME \
    github_token=$GITHUB_TOKEN \
    maven_base_url=$MAVEN_BASE_URL \
    maven_user=$MAVEN_USER \
    maven_token=$MAVEN_TOKEN \
    org=.org
```

This will add the necessary files to your rug archive and enable the
build in Travis CI.  Commit the changes made by the Editor and push
the commit to GitHub.  This will trigger the build of your Rug archive
and publish it to the `$MAVEN_BASE_URL/rugs-dev` repository.

To release your rug archive to `$MAVEN_BASE_URL/rugs-release`, push
a [semantic version][semver] tag, i.e., a tag of the form `1.2.3`, to
your Rug archive's GitHub repository.  Travis CI will then build the
tag and publish the archive to the rugs-release repository.

[cli]: https://github.com/atomist/rug-cli
[semver]: http://semver.org

### UpdateTravisMaven

The UpdateTravisMaven Editor overwrites the `.settings.xml` and
`travis-build.bash` files in a [Maven][mvn] project with the latest
standard versions.

[mvn]: https://maven.apache.org/

#### Prerequisites

Before running this Editor, you must have the following prerequisites
satisfied.

*   A GitHub repository for a project built with Maven cloned locally.

#### Parameters

This Editor has no parameters.

#### Running

Run it as follows:

```
$ cd to/your/rug/archive/repo
$ rug edit atomist-rugs:travis-editors:UpdateTravisMaven
```

If the project is a Maven project, this will upsert `.settings.xml`
and `travis-build.bash` files in the top-level directory of the
project.  If you are happy with the change, commit the changes made by
the Editor and push the commit to GitHub.

## Support

General support questions should be discussed in the `#support`
channel on our community slack team
at [atomist-community.slack.com][slack].

If you find a problem, please create an [issue][].

[issue]: https://github.com/atomist-rugs/travis-editors/issues

## Development

You can build, test, and install the project locally with
the [Rug CLI][cli].  Before running the Rug CLI and after any changes
to the `.atomist/package.json` file, you should ensure all of the
dependencies are available locally by running `npm install`.

[cli]: https://github.com/atomist/rug-cli

```sh
$ cd .atomist
$ npm install
$ rug test
$ rug install
```

To create a new release of the project, simply push a tag of the form
`M.N.P` where `M`, `N`, and `P` are integers that form the next
appropriate [semantic version][semver] for release.  For example:

```sh
$ git tag -a 1.2.3
```

The Travis CI build (see badge at the top of this page) will
automatically create a GitHub release using the tag name for the
release and the comment provided on the annotated tag as the contents
of the release notes.  It will also automatically upload the needed
artifacts.

---
Created by [Atomist][atomist].
Need Help?  [Join our Slack team][slack].

[atomist]: https://www.atomist.com/
[slack]: https://join.atomist.com/
