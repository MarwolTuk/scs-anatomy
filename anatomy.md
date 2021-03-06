# Anatomy of SCS-Files

## Preface
Geting my way into skinning/modding for ETS2 I decided to write down everything I get to know about the various resources. For my own reference, but also for others and as a store for all that information found all over the internets.

If you have some Information to share, I did not include, I most probably didn't found it yet, and would like to include. So, just leave a reply. :-)

## SCS-Files themselves
The `.scs` files seem to be archives either in a format called `hashfs` or `zipfs` being renamed zip-files. They all store parts of the same 'File-Hirarchy', with scs-files loaded first providing a base and those loaded after extending on it. The later ones are able to override information stored in firsts, if they contain a file in the same path as the earlier one.

### Formats
`hashfs` is the format the 'orginal' scs-files are in. They can be extracted using the (famous) [scs_extractor.exe](https://modding.scssoft.com/wiki/Documentation/Tools/Game_Archive_Extractor) you can get from the SCS Modding Wiki. Also I've found no way yet to create those archives, there must be one since some of the promods scs-files are in the same format.

`zipfs` on the other hand do seem to be just renamed zip-files. I've found no real evidence jet which compression and zip settings ETS2 supports, and which are problematic. If you have trouble creating those, this document may be beyond your scope.

### Load Order
On my ETS2 installation the load order of the base game seems to be (according to `game.log.txt`)
  * `effect.scs`
  * `core.scs`
  * `def.scs`
  * `locale.scs`
  * `base_cfg.scs`

Followed by those of the DLCs (seems to be in alphabetic order; may differ if you've got more DLCs installed)
  * `dlc_balkan_e.scs`
  * `dlc_balt.scs`
  * `dlc_daf_tuning_pack.scs`
  * `dlc_east.scs`
  * `dlc_fr.scs`
  * `dlc_goodyear.scs`
  * `dlc_heavy_cargo.scs`
  * `dlc_it.scs`
  * `dlc_krone.scs`
  * `dlc_michelin.scs`
  * `dlc_north.scs`
  * `dlc_oversize.scs`
  * `dlc_raven.scs`
  * `dlc_rims.scs`
  * `dlc_rocket_league.scs`
  * `dlc_schwarzmuller.scs`
  * `dlc_toys.scs`
  * `dlc_trailers.scs`

For mod scs-files the load order is defined by their order in the mod-manager, from top to bottom.

### File-Hierarchy
The structure inside the scs-files seems very well documented inside SCS own Modding Wiki under [Documentation/Engine/Game_data](https://modding.scssoft.com/wiki/Documentation/Engine/Game_data) so I won't repeat it here at whole. But because of the aim of this document, I'll be describing essential Parts.

## Defs
Once you have at lest one scs-file you'll find yourself in the file-hierarchy described in the last section. If it is a mod, it may contain a folder called `def`, on the base game, you have to extract `def.scs`, as my guessed by its name.

The structure and files there look like holding everything together. Their position and names seem to be well defined handles the game searches for. Most of the other stuff is referenced from there, directly or indirectly.

### sii-files
Unit serialized file - File storing serialized units 

### sui-files
Serialized unit include - The SII-include file without magic mark SiiNUnit 

## Objects

### pmd-files
Prism model descriptor - Storage of the model descriptor data

### Additional Filetypes used by [Prism3D](https://scssoft.com/technology)
All of this information is a copy of the Modding Wiki article [Documentation/Engine/Formats](https://modding.scssoft.com/wiki/Documentation/Engine/Formats) and only in here for short reference. If the need arises to describe any of those in deep, they get their own sections.

| Extension | Full name | Purpose | Type | 
|-----------|-----------|---------|------|
|.pmg | Prism model geometry         | Storage of the model geometry data          | Binary
|.pmc | Prism model collision        | Storage of the model dynamic collision data | Binary
|.pma | Prism model animation        | Storage of the model animation data         | Binary
|.ppd | Prism prefab descriptor      | Storage of the prefab data                  | Binary
|.pit | Prism intermediate trait     | Storage of the model traits                 | Text
|.pim | Prism intermediate model     | Storage of the model geometry data          | Text
|.pic | Prism intermediate collision | Storage of the model dynamic collision data | Text
|.pia | Prism intermediate animation | Storage of the model animation data         | Text
|.pip | Prism intermediate prefab    | Storage of the prefab data                  | Text 

## Textures

### mat-files
Material - Material definition
automat

### tobj-files
The Modding Wiki describes them as "Texture object - Texture detailed informations". tobj-files are binary files, containing one ore more strings, referencing dds-files by their absolute path in the file-hierarchy. There are rumors they also contain some information how the texture should be used (i.e. in which direction it should be repeated) but I couldn't find a hard clue yet.

A typical tobj-file looks like this (hexdumped):

    00000000  01 0a b1 70 00 00 00 00  00 00 00 00 00 00 00 00  |...p............|
    00000010  00 00 00 00 01 00 00 00  02 00 03 03 03 00 00 00  |................|
    00000020  00 00 00 00 00 01 00 00  1f 00 00 00 00 00 00 00  |................|
    00000030  2f 76 65 68 69 63 6c 65  2f 74 72 75 63 6b 2f 64  |/vehicle/truck/d|
    00000040  61 66 5f 78 66 2f 63 6f  6c 6f 72 2e 64 64 73     |af_xf/color.dds|

There are some sections the use of seems clear:

1. Byte `0x00` to `0x03`:  
   `01 0a b1 70` is the version of the tobj-file; the game complains inside `game.log.txt` about a wrong version of the tobj-file if set to any other value.

2. Byte `0x29`:  
   `1f` in our case. Contains the length of the string storing the path.

3. Byte `0x30` to the end:  
   ASCII data containing an already mentioned absolute path in the file-hierarchy to a dds-file.

### dds-files
dds-files store the underlying graphic data of the textures and are essentially images. There are extensions for GIMP or Photoshop to import/edit/export them. [Wikipedia](https://en.wikipedia.org/wiki/DirectDraw_Surface) looks like a good starting point to find more information about them.

For ETS2 you should create them with mipmaps and either in the RGB8 (R8G8B8) or RGBA8 color schema, depending on your need for an alpha channel.
