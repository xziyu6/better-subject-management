# Better Subject Management

A Victoria 3 mod that makes subject management more strategic and less exponentially punishing.

## Features

### Strategic Liberty Desire System

In vanilla Victoria 3, when you decrease the autonomy of any subject, **ALL** other subjects gain +10 liberty desire, making empire management exponentially harder as you acquire more subjects.

This mod changes the mechanic so that **only subjects with strategic interests in the same regions** as the targeted subject will increase their liberty desire. This creates a more strategic and geographically-aware subject management system.

### How It Works

When you reduce a subject's autonomy, other subjects will only gain liberty desire (+10) if they meet one of these conditions:

1. They have an **interest marker** in a strategic region where the targeted subject owns states
2. They **own states** in the same strategic region as the targeted subject

This means:
- ✅ A subject in India will care if you reduce autonomy of another subject in India
- ✅ A subject interested in China will care if you reduce autonomy of a Chinese subject
- ❌ A subject in Africa won't care about autonomy changes in South America
- ❌ A subject with no regional overlap won't be affected

## Compatibility

- **Game Version:** Victoria 3 v1.12.x
- **Mod Type:** Replaces diplomatic action using 1.12 modding system
- **Multiplayer:** Yes (synchronized)
- **Achievements:** Compatible (Victoria 3 doesn't disable achievements with mods)

## Installation

### Steam Workshop (Recommended)
*Coming soon*

### Manual Installation

1. Download or clone this repository
2. Copy the `mod` folder contents to:
   ```
   Documents\Paradox Interactive\Victoria 3\mod\better-subject-management\
   ```
3. Enable the mod in the Victoria 3 launcher
4. Start a new game or load an existing save

## Technical Details

### Files Modified
- `common/diplomatic_actions/bsm_subjects_decrease_autonomy.txt` - Replaces `da_decrease_autonomy`
- `common/scripted_triggers/bsm_subject_triggers.txt` - Helper triggers for regional checks
- `localization/english/bsm_subjects_l_english.yml` - Custom tooltip text

### Script Logic

The mod uses Victoria 3's strategic region system to determine overlapping interests:

```vic3
# Check for interest markers in target's regions
any_interest_marker = {
    scope:target_country = {
        any_scope_state = {
            region = scope:checking_marker.region
        }
    }
}

# OR check for states in same regions
any_scope_state = {
    scope:target_country = {
        any_scope_state = {
            region = scope:subject_region
        }
    }
}
```

## Development

This mod uses the Victoria 3 v1.12+ modding system with REPLACE/INJECT keywords.

### Building from Source

The mod is already in the correct structure. Just copy the `mod` folder to your Victoria 3 mod directory.

### Testing

1. Enable debug mode: Launch with `-debug_mode` flag
2. Use console commands to test scenarios
3. Check `error.log` in `Documents\Paradox Interactive\Victoria 3\logs\`

## Changelog

### v1.0.0 (Initial Release)
- Implemented strategic region-based liberty desire system
- Only affected subjects with overlapping regional interests increase liberty desire
- Full 1.12 compatibility using REPLACE keyword

## Credits

- Developed by xziyu6
- Thanks to the Victoria 3 modding community for documentation and examples

## License

This mod is released under the MIT License. See LICENSE file for details.

## Support

If you encounter any issues:
1. Check the `error.log` file
2. Verify you're using Victoria 3 v1.12 or later
3. Report issues on GitHub with your error.log and game version
- [Folder Structure](#folder-structure)
- [Further Resources](#further-resources)

# Setup

## Creating your own Repository

To set up a new repository using this template,
click on the "Use this Template" button on GitHub:
![button-example](documentation/template-guide.png)

## Checkout your new Repository

It is recommended to not check out the repository into the games mod folder.
If you are new to git and GitHub please read up on how to do this.

## Adjusting Mod Metadata

You can set up your mod's metadata in one of two ways:

**Manual approach:**
Edit the [mod/.metadata/metadata.json](mod/.metadata/metadata.json) file directly. The important things to change are:
- name
- id
- short_description

> ⚠️ **Important:** If you choose the manual approach, you must also delete or manually adjust the following files:
> - `mod/events/bsm_events.txt`
> - `mod/common/on_actions/bsm_on_actions.txt`

**Automated via GitHub Action:**

You can automatically initialize your mod's metadata and abbreviation using the included GitHub Actions workflow.  
This workflow not only sets up your abbreviation, but also:

- Generates a unique global variable (e.g., `on_mod_loaded_<your_abbreviation>`) to improve compatibility with other mods.
- Suppresses errors related to this variable, so you won't see unnecessary warnings during development, since you typically won't use it directly within your own mod.

This automation streamlines setup and helps avoid common issues.

**To use the workflow:**

1. **Enable write permissions for the workflow in the repository settings**
  - Go to your repository's **Settings**.
  - Under **Actions > General**, locate **Workflow permissions**.
  - Select **Read and write permissions** and click **Save**.

2. **Run the `Initialize Mod Template` workflow**
  - Open the **Actions** tab in your repository.
  - Select the **`Initialize Mod Template`** workflow.
  - Start the workflow and enter your desired abbreviation or prefix. Fill in any optional fields as needed.
  - When the workflow finishes, check [mod/events/abbreviation_event.txt](mod/events/abbreviation_event.txt):
    - The first line should now use your abbreviation instead of `bsm_error_suppression`.

For a visual guide to the workflow options, see the screenshot in `documentation/workflow.png`.

Using this workflow ensures your mod's abbreviation is set up correctly and your files are ready for development.

## Adding your Mod to the Game

To add the mod to the game/launcher we need to link it in the games mod folder.

### Windows

This can best be done via a folder junction. To create a new junction, we can use this command:
```
mklink /j "X:\path\to\Documents\Paradox Interactive\Victoria 3\mod\your-mod-folder" "X:\path\to\the\mod\directory\in\the\repo"
```
Important is that we point not to the repository but to the [mod](mod) subfolder.

The command can be run by either opening a Terminal or by pressing Win + R and putting the adjusted command into the run window.

**NOTE:** You need to replace the paths with the corresponding ones for your installation

### Linux

```
ln -s "~/path/to/mod/directory/in/the/repo" "~/.local/share/Paradox Interactive/Victoria 3/mod/your-mod-folder"
```

## Open the Launcher

In the Paradox Launcher, your mod should now show up and can be added to a playset as normal.

## Final Adjustments

It is recommended to replace the thumbnails before releasing your mod to steam.

There are two thumbnails for Victoria 3 mods:
- [Steam Workshop Thumbnail](mod/thumbnail.png)
- [Paradox Launcher Thumbnail](mod/.metadata/thumbnail.png)

# Folder Structure

This Repository is designed so you do not release your documentation or git folders to steam.

The [documentation](documentation) folder is meant for documentation
you may have for your mod like Markdown files or other types of documents.

The [image](image) folder is meant for reference images like original size images
that you used to create game images or templates for game images.
Included in this repository are:
- Template images for game logos of different types
- GIMP files to easily create Production Method and Building icons

The [mod](mod) folder contains the mod itself and is where you generally work in.

Finally, the [script](script) folder is meant for bash or batch scripts that you
developed for automating tasks for your mod.
This repository contains a script to copy your localization files from one language
to another.
That is helpful if you develop your mod in a base language like english and still want
your players to be able to use the mod in other languages.

# Further Resources

## Victoria 3 Wiki

The Victoria 3 Wiki can be outdated at times when it comes to modding,
but it is still a great resource and starting point for new modders.

https://vic3.paradoxwikis.com/Modding

## Victoria 3 Modding Coop

The Victoria 3 Modding Coop is a community maintained Discord server
that is quite active and can answer a lot of potential questions about
how to do specific things.

https://discord.com/invite/uUbuMTQjA7
