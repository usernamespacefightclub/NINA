<img width="630" height="514" alt="image" src="https://github.com/user-attachments/assets/b8d39352-7168-445f-a3da-df12ece97e1a" />

This project is based on the work of the late great Mootilda. Nothing I did here would have been possible without the legacy of the original Clean Installer. She is the most important person here.

NINA is an independent reimplementation based on the file format specification. In other words: instead of taking the original code and just changing the language, the program was built from scratch by understanding how `.sims2pack` files are actually structured internally — and then recreating, in its own way, a smaller and simpler version focused on properly reading and installing these files, without all the extra weight of screens, dependencies, and side features.

The utility is written in Python and uses libadwaita, with a modern interface and polished UX. It supports installing multiple packages at once. In testing, **one hundred** lots were installed in a single run without a single hiccup. (I have no idea what the actual limit is, so feel free to absolutely go nuts.)

---

# AI Usage

This application was made using AI. I am not a programmer — I'm still learning, and very little of what was made here was actually made by a human. So I strongly encourage people to fork this and keep working on it, because I honestly don't know how long I'll keep maintaining NINA myself.

I used Claude, KIMI, and Gemini. And the AGENTS.MD PONYTAIL.

I'm also more than happy if an actual developer wants to jump in as a contributor bc i have other projects for the game on Linux in mind for the future. Or fork it to continue as manteiner, idk how long i will to that by myself.

---

# How to install

NINA **does not have a pre-built installer** (no Flatpak, no AppImage, no distro package). You download the source code and run it directly: it's just Python. 

## 1. Install the dependencies

NINA needs Python 3, GTK 4, libadwaita, PyGObject (the bridge between Python and GTK), and `defusedxml`. Copy the command for your distro:

**Fedora / Nobara**
```bash
sudo dnf install python3-gobject gtk4 libadwaita python3-defusedxml
```

**Ubuntu / Debian / Mint**
```bash
sudo apt install python3-gi gir1.2-gtk-4.0 gir1.2-adw-1 python3-defusedxml
```

**Arch / Manjaro / EndeavourOS**
```bash
sudo pacman -S python-gobject gtk4 libadwaita python-defusedxml
```

**openSUSE**
```bash
sudo zypper install python3-gobject typelib-1_0-Gtk-4_0 typelib-1_0-Adw-1 python3-defusedxml
```

Package names may vary slightly between versions. If NINA complains about `gi`, `Gtk`, `Adw`, or `defusedxml` when you launch it, something from this list is probably missing.

## 2. Download NINA

With git:

```bash
git clone https://github.com/usernamespacefightclub/NINA.git
cd NINA
```

Or, without git: on GitHub, click **Code → Download ZIP**, extract it, and enter the folder.

## 3. Run it

Inside the NINA folder:

```bash
python3 gui.py
```

To open a package directly:

```bash
python3 gui.py /path/to/file.sims2pack
```

That's already enough to use it. The steps below are optional: they make NINA appear in your application menu and let it open automatically when you double-click a `.sims2pack`.

## 4. Add it to the application menu and associate `.sims2pack` files

Run these commands **from inside the NINA folder**. They create three files in your user directory (nothing is installed system-wide, so you don't need `sudo`).

```bash
mkdir -p ~/.local/share/applications ~/.local/share/mime/packages ~/.local/share/icons/hicolor/512x512/apps

cp NINA_PLACEHOLDER_ICON.png ~/.local/share/icons/hicolor/512x512/apps/org.gnome.Nephilim.png

cat > ~/.local/share/applications/org.gnome.Nephilim.desktop <<EOF
[Desktop Entry]
Type=Application
Name=NINA pack installer
Comment=Install .Sims2Pack files into The Sims 2
Exec=python3 "$PWD/gui.py" %F
Icon=org.gnome.Nephilim
Terminal=false
Categories=Game;Utility;GTK;
MimeType=application/x-sims2pack;
EOF

cat > ~/.local/share/mime/packages/org.gnome.Nephilim.xml <<'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<mime-info xmlns="http://www.freedesktop.org/standards/shared-mime-info">
  <mime-type type="application/x-sims2pack">
    <comment>The Sims 2 package</comment>
    <glob pattern="*.sims2pack" case-sensitive="false"/>
  </mime-type>
</mime-info>
EOF

update-mime-database ~/.local/share/mime
update-desktop-database ~/.local/share/applications
xdg-mime default org.gnome.Nephilim.desktop application/x-sims2pack
gtk-update-icon-cache -f -t ~/.local/share/icons/hicolor 2>/dev/null || true
```

Done. NINA should now appear in your application menu and under **Open With** for `.sims2pack` files. If it doesn't, log out and back into your session.

Important: the menu entry points to the folder where you downloaded NINA. **If you move or delete that folder, you need to repeat this step** (or remove the files as explained below).

## Updating

If you downloaded it with git, run `git pull` inside the folder. If you downloaded the ZIP, download it again and replace the folder (and repeat step 4 if you moved it somewhere else).

## Uninstalling

```bash
rm -f ~/.local/share/applications/org.gnome.Nephilim.desktop
rm -f ~/.local/share/mime/packages/org.gnome.Nephilim.xml
rm -f ~/.local/share/icons/hicolor/512x512/apps/org.gnome.Nephilim.png
update-mime-database ~/.local/share/mime
update-desktop-database ~/.local/share/applications
rm -rf ~/.config/nephilim
```

Then delete the NINA folder.

The second-to-last command removes your settings (including the Sims 2 path you selected). Packages NINA has already installed into the game **stay there**: NINA does not uninstall them.

---

# TRIVIA

## What is a `.sims2pack`?

Think of it as a sealed box. Inside are the items, with a label on the outside telling you what each item is. That's a `.sims2pack` file.

The game can't open this box by itself: someone has to open it, read the label, and put each item in the correct drawer inside the The Sims 2 folder.

## What NINA does

NINA is that someone.

You point it at one or more packages, it opens the boxes, shows you what's inside, and puts everything where it belongs:

- **Lots** (houses and lots) go into the `Teleport` folder, where the game imports them the next time it starts.
- **Custom Content** (CC included with the package) goes into the `Downloads` folder.

If a package contains something that changes the game's rules (a hack), NINA warns you before installing it, so you can make the decision with your eyes open.

## What's the difference between this and Clean Installer?

Sims2Pack Clean Installer (S2PCI) is the original tool, created by Karol Rybak in 2004 and later maintained by Mootilda until 2014. It is the program that taught the entire community how to install lots without making a mess of the game, and NINA would not exist without it.

NINA is not a copy of it, nor is it a port: the AI understood how the file is structured internally and wrote a new program, my way.

The practical differences:

- **The original is Windows-only.** It relies on the Windows registry and .NET. NINA is made for Linux: it opens like any other application, with no Wine involved, and you simply tell it where your game folder is (the one inside Lutris/Wine).
- **Smaller and leaner.** The original comes with its own installer, online list updates, a Windows Explorer menu extension, and a RAR reader. NINA does one thing: read and properly install packages.
- **Multiple packages at once**, queued up without having to open them one by one.
- **Modern UI.** It uses GTK4 and libadwaita, the look used by modern GNOME applications. There is also a "classic" table view for anyone who misses the original.

In short: Clean Installer is the reference. NINA is the version made specifically for people who play The Sims 2 on Linux.

---

Soon I'll open up ways to support me.

Keep in mind that I made all of this over the course of a few months while suffering through the free tiers of Claude, Kimi, and Gemini.

The available languages ​​were chosen based on empirical observation of which languages ​​are most common within the community, as well as through research. It is unlikely that any additional languages ​​will be added.

I don't really know how GitHub works, so sorry if I did something wrong. Everything here looks confusing. That's why I encourage an actual programmer to fork or get the project transfred even more.

Thank you.
