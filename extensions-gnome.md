# Installation Extensions Gnome Manually

* Go to and navigate to [extensions.gnome.org](https://extensions.gnome.org/) and find a necessary extension.
* Choose gnone-shell and extension version
* Extract archive (.zip)
* In the folder find "metadata.json" file open it with text editor
* Find "uuid", copy text betwween quotes
* Rename extracted folder with copied text
* If doesn't exist, create extensions folder: `mkdir -p ~/.local/share/gnome-shell/extensions`
* Move folder to newly created folder
* Install `gnome-tweaks`
* Log out & Log in
* Open `gnome-tweaks` go to `Extensions` and enable extension

