# LinkStitcher AGLDWG Drupal theme

This theme is based on the Drupal Bartik theme.

There's an implementation aim to have as few files in this repository
as necessary. So, as far as possible, this theme defers to Bartik.

## Creating the block configuration

Since Drupal 9.4, Bartik is no longer the default Drupal theme.
Therefore, it's now necessary to include the block configuration in
the config/install directory. (It seems that if you don't otherwise
specify block configuration for a custom theme, installing the theme
means you get a block configuration created that is a copy of that of
the default theme. As long as Bartik was the default theme, we got
what we wanted.)

To create those files, copy in the corresponding files for Bartok from
a fresh Drupal installation:

```shell
cd config/install
cp <Drupal installation>/web/core/profiles/standard/config/optional/block.block.bartik_* .
```
Then make the necessary changes to create the files for this theme:

```shell
for f in block.block.bartik_* ; do
  field="${f/block.block.bartik_/}"
  field="${field/.yml/}"
  cp $f "block.block.agldwg_${field}.yml"
  id="agldwg_${field}"
  ID="$id" yq -i eval '.dependencies.theme=["agldwg"] | .id=env(ID) | .theme="agldwg"' "block.block.agldwg_${field}.yml"
done
```

Then make some additional changes specific to this theme:

```shell
# Move the user account menu to the sidebar.
yq -i eval \
  '.region="sidebar_first" | .settings.label_display="visible"' \
  block.block.agldwg_account_menu.yml

# Hide the breadcrumbs, because they're often wrong/misleading, and
# we aren't going to fix them (yet).
yq -i eval '.status=false' block.block.agldwg_breadcrumbs.yml
```
