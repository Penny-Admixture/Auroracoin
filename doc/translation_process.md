Translations
============

The DigiByte-Core project has been designed to support multiple localisations. This makes adding new phrases, and completely new languages easily achievable. For managing all application translations, DigiByte-Core makes use of the Transifex online translation management tool.

### Helping to translate (using Transifex)
Transifex is setup to monitor the GitHub repo for updates, and when code containing new translations is found, Transifex will process any changes. It may take several hours after a pull-request has been merged, to appear in the Transifex web interface.

Multiple language support is critical in assisting DigiByte’s global adoption, and growth. One of DigiByte’s greatest strengths is cross-border money transfers, any help making that easier is greatly appreciated.

See the [Transifex Bitcoin project](https://www.transifex.com/bitcoin/bitcoin/) to assist in translations. You should also join the translation mailing list for announcements - see details below.

### Writing code with translations
We use automated scripts to help extract translations in both Qt, and non-Qt source files. It is rarely necessary to manually edit the files in `src/qt/locale/`. The translation source files must adhere to the following format:
`digibyte_xx_YY.ts or digibyte_xx.ts`

`src/qt/locale/digibyte_en.ts` is treated in a special way. It is used as the source for all other translations. Whenever a string in the source code is changed, this file must be updated to reflect those changes. A custom script is used to extract strings from the non-Qt parts. This script makes use of `gettext`, so make sure that utility is installed (ie, `apt-get install gettext` on Ubuntu/Debian). Once this has been updated, `lupdate` (included in the Qt SDK) is used to update `digibyte_en.ts`.

To automatically regenerate the `digibyte_en.ts` file, run the following commands:
```sh
cd src/
make translate
```

`contrib/digibyte-qt.pro` takes care of generating `.qm` (binary compiled) files from `.ts` (source files) files. It’s mostly automated, and you shouldn’t need to worry about it.

**Example Qt translation**
```cpp
QToolBar *toolbar = addToolBar(tr("Tabs toolbar"));
```

### Creating a pull-request
For general PRs, you shouldn’t include any updates to the translation source files. They will be updated periodically, primarily around pre-releases, allowing time for any new phrases to be translated before public releases. This is also important in avoiding translation related merge conflicts.

When an updated source file is merged into the GitHub repo, Transifex will automatically detect it (although it can take several hours). Once processed, the new strings will show up as "Remaining" in the Transifex web interface and are ready for translators.

To create the pull-request, use the following commands:
```
git add src/qt/digibytestrings.cpp src/qt/locale/digibyte_en.ts
git commit
```

### Creating a Transifex account
Visit the [Transifex Signup](https://www.transifex.com/signup/) page to create an account. Take note of your username and password, as they will be required to configure the command-line tool.

You can find the Bitcoin translation project at [https://www.transifex.com/bitcoin/bitcoin/](https://www.transifex.com/bitcoin/bitcoin/).

### Installing the Transifex client command-line tool
The client is used to fetch updated translations. If you are having problems, or need more details, see [https://docs.transifex.com/client/installing-the-client](https://docs.transifex.com/client/installing-the-client)

`pip install transifex-client`

Setup your Transifex client config as follows. Please *ignore the token field*.

```ini
nano ~/.transifexrc

[https://www.transifex.com]
hostname = https://www.transifex.com
password = PASSWORD
token =
username = USERNAME
```

The Transifex DigiByte project config file is included as part of the repo. It can be found at `.tx/config`, however you shouldn’t need to change anything.

### Synchronising translations
To assist in updating translations, we have created a script to help.

1. `python contrib/devtools/update-translations.py`
2. `git add` new translations from `src/qt/locale/`
3. Update `src/qt/auroracoin_locale.qrc` manually or via
```bash
git ls-files src/qt/locale/*ts|xargs -n1 basename|sed 's/\(auroracoin_\(.*\)\).ts/        <file alias="\2">locale\/\1.qm<\/file>/'
```
4. Update `src/Makefile.qt.include` manually or via
```bash
git ls-files src/qt/locale/*ts|xargs -n1 basename|sed 's/\(auroracoin_\(.*\)\).ts/  qt\/locale\/\1.ts \\/'
```
5. Update `build_msvc/libauroracoin_qt/libauroracoin_qt.vcxproj` or via
```bash
git ls-files src/qt/locale/*ts|xargs -n1 basename |
  sed 's/@/%40/' |
  sed 's/\(auroracoin_\(.*\)\).ts/    <None Include="..\\..\\src\\qt\\locale\\\1.ts">\n      <DeploymentContent>true<\/DeploymentContent>\n    <\/None>/'
```

Auroracoin doesn't use Transifex. To update existing language files to include new strings do the following:

```
make translate
```

This updates auroracoin_en.ts with the new strings, which will be used as a source reference. Next use this file
with each language file to update (replace <lang> with the language code):

```
cd src/qt/locale/
lconvert -locations relative -no-obsolete -i auroracoin_en.ts auroracoin_<lang>.ts -o auroracoin_<lang>.ts
```

**Do not directly download translations** one by one from the Transifex website, as we do a few post-processing steps before committing the translations.

### Handling Plurals (in source files)
When new plurals are added to the source file, it's important to do the following steps:

1. Open `digibyte_en.ts` in Qt Linguist (included in the Qt SDK)
2. Search for `%n`, which will take you to the parts in the translation that use plurals
3. Look for empty `English Translation (Singular)` and `English Translation (Plural)` fields
4. Add the appropriate strings for the singular and plural form of the base string
5. Mark the item as done (via the green arrow symbol in the toolbar)
6. Repeat from step 2, until all singular and plural forms are in the source file
7. Save the source file

### Translating a new language
To create a new language template, you will need to edit the languages manifest file `src/qt/digibyte_locale.qrc` and add a new entry. Below is an example of the English language entry.

```xml
<qresource prefix="/translations">
    <file alias="en">locale/digibyte_en.qm</file>
    ...
</qresource>
```

**Note:** that the language translation file **must end in `.qm`** (the compiled extension), and not `.ts`.

### Questions and general assistance
The DigiByte-Core translation maintainers include *tcatm, seone, Diapolo, wumpus and luke-jr*. You can find them, and others, in the Freenode IRC chatroom - `irc.freenode.net #digibyte-core-dev`.

If you are a translator, you should also subscribe to the mailing list, https://groups.google.com/forum/#!forum/digibyte-translators. Announcements will be posted during application pre-releases to notify translators to check for updates.
