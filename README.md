Linux Audio base extension
==========================

This is a base extension for Flatpak Linux audio plugins to allow
building Linux audio plugins flatpaks.

**This package only provides a definition of the extension points which
are a prerequisite to build the plugins.** It doesn't provide anything
else, and applications that want to use audio plugins do not need to
use it. Just copy the extension point definitions to your manifest.

It currently supports:

- LV2  extension.
- LADSPA extension.
- VST (Linux) extension.
- DSSI
- VST3

All the above uses `org.freedesktop.LinuxAudio.Plugins` extension mount
point.


Content
-------

This is empty, it only provide the needed declaration for the
extension points.

Application using audio plugins
-------------------------------

Your application supports audio plugins?

Add the unified extension point for plugins:
```
"add-extensions": {
  "org.freedesktop.LinuxAudio.Plugins": {
    "directory": "extensions/Plugins",
    "version": "21.08",
    "add-ld-path": "lib",
    "merge-dirs": "ladspa;dssi;lv2;lxvst;vst3",
    "subdirectories": true,
    "no-autodownload": true
  }
}
```

The manifest of this flatpak has them all.

Also make sure the application find the plugins. As an example to find
LV2 put the following finish argument:

```
"--env=LV2_PATH=$HOME/.lv2:/app/extensions/Plugins/lv2"
```

For DSSI, LADSPA, VST and VST3 it is the same change as above, see the
table below for a summary.

```
"--env=DSSI_PATH=/app/extensions/Plugins/lv2"
"--env=LADSPA_PATH=/app/extensions/Plugins/lv2"
"--env=VST_PATH=/app/extensions/Plugins/lxvst"
"--env=VST3_PATH=$HOME/.vst3:/app/extensions/Plugins/vst3"
```

The table below summarize the mount point and environment to set. The
mount point is `/app/extensions/Plugins`. The subdir is a subdirectory
in the mount point that will have all the plugins as needed by the
application host.


Format     | subdir | env
-----------|--------|--------------
LV2        | lv2    | `LV2_PATH`
DSSI       | dssi   | `DSSI_PATH`
LADSPA     | ladspa | `LADSPA_PATH`
VST (Linux)| lxvst  | `LXVST_PATH` or `VST_PATH`
VST3       | vst3   | `VST3_PATH`

**Note:** LV2 must have `$HOME/.lv2` in the LV2_PATH due to the VL2
specification.  As for VST3, it's optional and you mileage may vary,
but it allow installing VST3 locally and be visible from inside the
flatpak sandbox.

Runtime considerations
----------------------

Currently the base runtime is Freedesktop 20.08. Plugins and
applications have to use the same runtime so you should consider this
when upgrading the runtime on your application flatpak.

As of writing 19.08 is deprecated.

When moving to a more recent Freedestkop, branches will have to be
created to keep the older versions of the plugins available. As an
application you might want to consider when you want to do the
upgrade.

The `version` specified for the extension point of the application has
to match the underlying freedesktop SDK it uses and plugins are built
using the `branch` with the same name. This will allow transitioning
to a new runtime without breaking applications using the older
runtime.

Plugins
-------

You want to provide a Plugin as a Flatpak package? Build a
an extension, using the base app.

`org.freedesktop.LinuxAudio.Plugins` is the base id for the plugin.
