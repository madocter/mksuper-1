# mksuper

Tools and scripts to **extract, modify and rebuild the `super.img` partition** for **Unihertz devices**, allowing you to generate flashable binaries using a **stock ROM** and a **GSI (LineageOS / Gargoyle)**.

This project is mainly used to build custom `super.img` images for devices like the **Unihertz Titan Pocket**, but also supports other Unihertz models.

You may need to adjust the scripts or run the commands individually on your machine to get the desired results.

---

## Requirements
- Python **3.10.18**
- Linux (Ubuntu / Debian recommended)
- `sudo` privileges (to install dependencies)
- On Windows: **WSL + Ubuntu**

Suggestion: Use [pyenv](https://github.com/pyenv/pyenv) to switch between python versions. Example: `pyenv install 3.10`, `pyenv local 3.10`

Windows users: [Install WSL + Ubuntu](https://gist.github.com/rumplestilzken/186d1aaebf2d3927ddfae6f7619e5780#file-installing-ubuntu-on-wsl)

## Supported Devices
* Titan Pocket
* Titan Slim
* Jelly 2E
* Atom L
* Tank

## Supported Image Formats
* .img
* .img.tar.gz
* .img.xz

## Download your appropriate version of stock firmware.
See [here](http://rumplestilzken.com:14005/Unihertz/StockResources)

## Download Repository
`git clone https://github.com/rumplestilzken/mksuper.git mksuper`

## Stock Rom
Place your downloaded stock rom in the mksuper directory to perform **extract.py**

The stock ROM can be:
- In **ZIP file** format which will be unziped automatically by the script (it detects first zip file inside mksuper directory)
- An already extracted directory example: **uniherzt-stock-rom** in this case specify the directory with `-stock uniherzt-stock-rom`

## Place your gargoyle image in the mksuper directory
Copy your new system image or archive into the mksuper directory.
Don´t worry the script will detect the binary automatically but can specify with option: -gsi gargoyle-pocket.img

## Run install-dependencies.py
This will ask for your admin password, it is installing apt packages, inspect the script if you have an issue and run the commands yourself.

```bash
    python install-dependencies.py
```

## Run extract.py
This will extract the super, system, vendor and product partitions, then copy them for update

```bash
    python extract.py
```

*This command asumes the stock rom to be in a ZIP file inside mksuper directory.

### Options for extract.py

```bash
    python3 extract.py -stock uniherzt-stock-rom
```

*This command in case you have stock binaries already unziped in a directory such as `uniherzt-stock-rom`

## mksuper
This will package the `super.new.img` to flash in to your device

```bash
python mksuper.py
```

By default: bvN (No GAPPS), garygole GSI first *img file inside mksuper directory, device autodetect.

### Options for mksuper
Example command for uniherzt titan pocket and bgN version of gargoyle GSI.

```bash
python mksuper.py -gsi gargoyle-pocket.img -dev pocket -no-product
```

**Parameters**
* `-dev` specify manually the device, possible options: [`pocket, slim, tank, tank_mini, jelly2e, atoml, jellymax`]
* `-no-product` if using a Garygole GSI image of type bgN ( with GAPPS, google apps)
* `-gsi` to specify clearly the garygole GSI image, if not provided, the script will Use the first .img found in the directory and assume it is the Gargoyle / LineageOS image.

#### Other options

**Repack stock system (no GSI)**

`python3 mkuser.py -repack`

This rebuilds super.img using only stock partitions.

**Show lpmake command only (debug)**

`python3 mkuser.py -command`

Prints the full lpmake command
Does not generate super.img
Useful for debugging, manual tuning, or running lpmake yourself.


### 📤 Output
`super/super.new.img`

Final flashable super image.

***NOTE***

**Titan Slim** and **Titan Pocket** need those images to be flashed as well not only the super, take them from stock directory:

`vbmeta.img`  
`vbmeta_system.img`  
`vbmeta_vendor.img`


***Note about lpmake warnings***

During super.img creation you may see messages like:  
`Invalid sparse file format at header magic`


This is normal and expected.

lpmake attempts to detect whether input images are Android sparse images.
If an image is a raw/ext4 image (which is the case for stock-extracted partitions and most GSIs), the sparse header check fails and this warning is printed.

The warning:

Does not indicate a failure  
Does not corrupt the output  
Can be safely ignored

As long as super.new.img is created successfully and no fatal errors are reported, the image is valid and flashable.