# normalize-profile

This tool reads Apple's provisioning profile files and produces
reproducible output perfect for diffing. You can easily integrate this
with `git diff` to automatically normalize these when diffing commits
containing profiles.

## Installation

Install with [homebrew](https://brew.sh):

```bash
brew install keith/formulae/normalize-profile
```

Or put `normalize-profile` somewhere in your `$PATH` and make sure you
have `python3` installed.

## Usage

Output a profile with all contents normalized:

```bash
normalize-profile foo.mobileprovision
```

Ignore some fields that always changes and don't always provide useful
information:

```bash
normalize-profile --ignore-field UUID --ignore-field CreationDate foo.mobileprovision
```

Ignore all the fields that always change and you have no control over
(note this could mean you see a file has changed with `git status` and
yet running `git diff` shows no output. In the case of `ExpirationDate`
you might still need to check in the updated profile):

```bash
normalize-profile --ignore-noisy-fields foo.mobileprovision
```

Shorten certificate data to reduce diff size (useful for large diffs):

```bash
normalize-profile --shorten-certs foo.mobileprovision
```

Ignore profiles that are invalid for some reason:

```bash
normalize-profile --ignore-invalid foo.mobileprovision
```

See all current options with `normalize-profile --help`

## Git integration

First you need to tell git to treat `mobileprovision` files differently
by adding it to your global
[`attributesfile`](https://git-scm.com/docs/gitattributes):

If you already have a global `attributesfile` you can see it's path
with:

```bash
git config --global core.attributesfile
```

If you do add this line to the existing file:

```
*.mobileprovision diff=provision
```

Otherwise create a new file with this contents and configure git to read
it:

```bash
echo "*.mobileprovision diff=provision" >> ~/.gitattributes
git config --global core.attributesfile ~/.gitattributes
```

The configure git with the command to use:

```bash
git config diff.provision.textconv normalize-profile
```

NOTE: You can add any of the flags from the [Usage](#usage) section to
this command like this:

```bash
git config --global diff.provision.textconv "normalize-profile --ignore-noisy-fields"
```

To remove this config run:

```bash
git config --global --remove-section diff.provision
```
