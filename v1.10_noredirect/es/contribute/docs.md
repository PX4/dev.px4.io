# Contributing to Documentation

Contributions to the guides for all parts of the Dronecode project are very welcome. This includes the PX4 and QGroundControl developer and user guides, and the MAVLink guide. This article explains how you can make changes, add content, and create translations.

> **Note** You will need a (free) [Github](http://github.com) account to contribute to the guide.

## Quick Changes

Fixing typos or editing an *existing page* is easy:

1. Click the **Edit** toolbar icon at the top of the relevant page in the guide.
    
    ![Gitbook: Edit Page button](../../assets/gitbook/gitbook_toolbar_icon_edit.png)
    
    This will open the page for editing (in Github).

2. Make the desired change.

3. At the bottom of the page you'll be prompted to create a separate branch and then guided to submit a *pull request*.

The documentation team reviews submitted pull requests and will either merge it or work with you to update it.

## Adding New Content - Big Changes

If you want to add new pages or images that can't easily be done through the Github interface. In this case you make changes in the same way as you would for code changes: use the *git* toolchain to get the code, modify it as needed, test that it renders properly using the Gitbook client, create a branch for your changes and create a pull request (PR) to pull it back into the documentation. The following instructions explain how.

### What Goes Where?

The *Developer Guide* is for documentation that is relevant to *software developers*. This includes users who need to:

* Add or modify platform features - modules, flight modes, etc.
* Add support/integrate with new hardware - flight controllers, peripherals, airframes, etc.
* Communicate with the platform from an external source - e.g. a companion computer.
* Understand the architecture

The *User Guide*, by contrast, is *primarily* for users who want to:

* Fly a vehicle using PX4
* Build, modify, or configure a vehicle using PX4 on a supported/existing airframe.

> **Tip** For example, detailed information about how to build/configure an existing airframe are in the User Guide, while instructions for defining a *new* airframe are in the Developer Guide.

### Gitbook Documentation Toolchain

The guide uses the [Legacy Gitbook Toolchain](https://legacy.gitbook.com/) toolchain.

Change requests can be either done on the Gitbook website using the [Gitbook editor](https://gitbookio.gitbooks.io/documentation/content/editor/index.html) or locally (more flexible, but less user-friendly).

In order to contribute many changes to the documentation, it is recommended that you follow these steps to add the changes locally and then create a pull request:

* [Sign up](https://github.com/join) for github if you haven't already
* Fork the PX4 user guide from [here](https://github.com/PX4/px4_user_guide) or Dev guide from [here](https://github.com/PX4/Devguide). For instructions to fork a git repository, see [here](https://help.github.com/articles/fork-a-repo/#fork-an-example-repository).
* Clone your forked repository to your local computer  
        sh
        cd ~/wherever/
        git clone https://github.com/<your git name>/px4_user_guide.git

* Install gitbook via NPM. At the terminal prompt, simply run the following command to install GitBook:
    
    ```sh
    npm install gitbook-cli -g
    ```
    
    > **Note** Everything you need to install and build Gitbook locally is also explained in the [toolchain documentation](https://github.com/GitbookIO/gitbook/blob/master/docs/setup.md).

* Navigate to your local repository and add original upstream:
    
    ```sh
    cd ~/wherever/px4_user_guide
    git remote add upstream https://github.com/PX4/px4_user_guide.git
    ```

* Now you can checkout a new branch and add your changes. To build your book, run:
    
    ```sh
    gitbook build
    ```
    
    > **Note** If you run into an error: `/usr/bin/env: node: No such file or directory`, run `ln -s /usr/bin/nodejs /usr/bin/node`

* To preview and serve your book, run:
    
    ```sh
    gitbook serve
    ```
    
    > **Note** run `gitbook install` to install missing plugins.

* Now you can browse your local book on http://localhost:4000/

* Exit serving using `CTRL+c` in the terminal prompt.

* You can also serve on a different port instead of 4000:
    
    ```sh
    gitbook serve --port 4003
    ```

* You can also output as html, pdf, epub or mobi: 
        sh
        gitbook help

* Once you are satisfied with your changes after previewing them, you can add and commit them:
    
    ```sh
    git add <file name>
    git commit -m "<your commit message>"
    ```
    
    For a good commit message, please refer to [Contributing](../contribute/README.md) section.

* Now you can push your local commits to your forked repository
    
    ```sh
    git push origin <your feature branch name>
    ```

* You can verify that the push was successful by going to your forked repository in your browser: ```https://github.com/<your git name>/px4_user_guide.git```  
    There you should see the message that a new branch has been pushed to your forked repository.
* Now it's time to create a pull request (PR). On the right hand side of the "new branch message" (see one step before), you should see a green button saying "Compare & Create Pull Request". Then it should list your changes and you can (must) add a meaningful title (in case of a one commit PR, it's usually the commit message) and message (<span style="color:orange">explain what you did for what reason</span>. Check [other pull requests](https://github.com/PX4/px4_user_guide/pulls) for comparison)
* You're done! Responsible members of PX4 guides will now have a look at your contribution and decide if they want to integrate it. Check if they have questions on your changes every once in a while.

In overview:

* Pages are written in separate files using markdown \(almost the same syntax used by Github wiki\). 
* The *structure* of the book is defined in a file named **SUMMARY.md**.
* This is a [multilingual](https://github.com/GitbookIO/gitbook/blob/master/docs/languages.md) book, so there is a **LANGS.md** file in the root directory defining what languages are supported. Pages for each language are stored in the folder named for the associated language code \(e.g. "zh" for Chinese, "en" for English\). 
* A file named **book.json** defines any dependencies of the build.
* A web hook is used to track whenever files are merged into the master branch on this repository, causing the book to rebuild.

## Style guide

1. Files/file names

* Put new files in an appropriate sub-folder
* Use descriptive names. In particular, image filename should describe what they contain.
* Use lower case and separate words using underscores "\_"

2. Images

* Use the smallest size and lowest resolution that makes the image still useful.
* New images should be created in a sub-folder of **/assets/** by default (so they can be shared between translations).

3. Content:

* Use "style" \(bold, emphasis, etc\) consistently. **Bold** for button presses and menu definitions. *Emphasis* for tool names. Otherwise use as little as possible.
* Headings and page titles should use "First Letter Capitalisation"
* The page title should be a first level heading \(\#\). All other headings should be h2 \(\#\#\) or lower.
* Don't add any style to headings.
* Don't translate the *first part* of a note, tip or warning declaration (e.g. `> **Note**`) as this precise text is required to render the note properly.

## Translations {#translation}

We'd love your help to translate *QGroundControl* and our guides for PX4, *QGroundControl* and MAVLink. For more information see: [Translation](../contribute/translation.md).

## Licence

All PX4/Dronecode documentation is free to use and modify under terms of the permissive [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) licence.