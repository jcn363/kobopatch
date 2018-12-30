Gutting kobopatch.yaml
=================================================

· Since the system used in Kobo is Linux, the UNIX nomenclature is used (with / instead of Windows \ ).

· Indents / spaces (as long as they are not in a comment) matter.

· Everything that follows the symbol # is a comment.

    ## Works with kobopatch v0.7 or higher.
    ## You can update kobopatch by downloading the latest release from https://github.com/geek1011/kobopatch/releases.

· Words that precede two points, and carry text below, are variables, which hold the value that follow them. Like a dictionary, word and definition.

    version: 4.10.11655
    in: src/kobo-update-4.10.11655.zip
    out: out/KoboRoot.tgz
    log: out/log.txt
    
    patchFormat: kobopatch

· _Version_: it is the version of the firmware that we want to patch -> the version.

· _In_: compressed (with address) where the firmware we want to patch is located -> the firmware.

· _Out_: packed (with address) where the patched firmware will be saved ready for your application -> the patch.

· _Log_: information / reference file where commands, errors, etc. are stored while the patch creation -> the log.

· _PatchFormat_: is the format in which the patches are written -> kobopatch is the current one.

· Words that precede two points, but DO NOT carry text below, are sections.

· In the _patches_ section we find a list of relationships

    file with patches: file to patch

· In a file with patches there can only be patches which modify a only program or library. But there may be several files which modify the same program or library.

· Both _libnickel-PROGRESIVE.yaml_ and _libnickel.so.1.0.0.yaml_ contain patches which modify the same library: _libnickel.so.1.0.0_.

    patches:
        #  src/nickel-PROGRESIVE.yaml: usr/local/Kobo/nickel
        #  src/nickel-PROGRESIVE_Nova.yaml: usr/local/Kobo/nickel
          src/nickel-PROGRESIVE_NovaTO.yaml: usr/local/Kobo/nickel
        #  src/nickel.yaml: usr/local/Kobo/nickel
          src/sickel.yaml: usr/local/Kobo/sickel
          src/libadobe.so.yaml: usr/local/Kobo/libadobe.so
          src/libnickel-PROGRESIVE.yaml: usr/local/Kobo/libnickel.so.1.0.0
          src/libnickel.so.1.0.0.yaml: usr/local/Kobo/libnickel.so.1.0.0
          src/librmsdk.so.1.0.0.yaml: usr/local/Kobo/librmsdk.so.1.0.0

· In the _overrides_ section there is a list of files with patches (nameed in the _patches_ section) with the patches they contain and whether we want to apply them or not.

   The value assigned here overwrites the one that is inside the patch file (default is _no_).
   
   It is not mandatory, but it is very useful and convenient, since at a glance it allows us to see the patches that we have available along with their status. In addition it allows us to enable and disable patches (of different programs and libraries) without having to open the file which contains them and to look for them in it.
   
   To say we want to apply a patch called _PROGRESIVE_, which is inside the _nickel-PROGRESIVE.yaml_ file, to _nickel_ program, regardless of whether it is activated or not, we would have to put:
   
    overrides:
      src/nickel-PROGRESIVE.yaml:
        PROGRESIVE: yes

**WARNING**: following this example the previous lines would not do anything, since in the _patches_ section the line relative to _nickel-PROGRESIVE.yaml_ was commented and therefore the program does not know what file must patch.

    patches:
    #  src/nickel-PROGRESIVE.yaml: usr/local/Kobo/nickel

· There may be as many patches named as patches are into that file. As said, it is not mandatory that they all are listed, although it's very useful.

· The name of the patch must correspond exactly with the one that is inside the corresponding patch file.

· Remember that the value that rules is the one set here.

    src/libnickel-PROGRESIVE.yaml:
      Dictionary, remove long winded closest match notification: yes
      unclassified (PROGRESIVE): yes
      MY ePub/Kepub fixed font sharpness: yes
      Wikipedia Search language: yes
      MY KePub stylesheet additions: yes
      My Dictionary text font-family/font-size/line-height: yes

· There are patches that are self-excluding. If more than one is activated, at the same time, the program will stop, the patch will not be generated and we will have to go to the log to know where is the error that we have made.

    # PATCH GROUP - DON'T EANBLE MORE THAN 1
        Disable reading footer: no
        Custom reading footer style: yes
    # END GROUP

· There are patches that are divided into two parts because each one is applied to a different file.

    src/libnickel.so.1.0.0.yaml:
      Force user line spacing in ePubs (part 1 of 2): yes # PART 2 in librmsdk.so.1.0.0.yaml
    src/librmsdk.so.1.0.0.yaml:
      Force user line spacing in ePubs (Part 2 of 2): yes # PART 1 in libnickel.so.1.0.0.yaml

**NOTE**: As you can see, the comments do not necessarily have to be at the beginning of the line.

· If you want to translate the messages of the system that appear on the screen (let's say into _spanish_ for example), or simply to correct/change some of them (they have sometimes put _libri_ instead of _libros_), you can do it. To do this, you must install the _lrelease_ program and tell to the patch program where do you installed

    # lrelease: /path/to/lrelease

**NOTE**: I do not use it (it is commented) because I have it integrated in the system.

· In the translations section you must put the list of translations you want to include in the patch.

    file with your translation: file to include in the patch

    translations:
      src/translations/trans_es.ts: usr/local/Kobo/translations/trans_es.qm

· You may not want to translate but you have a file that you want to include / replace into the system (fonts, script files, etc.). That's what the _files_ section is for. Remember: "_a place for every thing and everything in its place_".

    file that you want to add to the patch: file with address in the device
    
    files:
      src/hyphenDicts/hyph_de.dic: usr/local/Kobo/hyphenDicts/hyph_de.dic
      src/hyphenDicts/hyph_es.dic: usr/local/Kobo/hyphenDicts/hyph_es.dic
      src/hyphenDicts/hyph_en_US.dic: usr/local/Kobo/hyphenDicts/hyph_en_US.dic
      src/hyphenDicts/hyph_fr.dic: usr/local/Kobo/hyphenDicts/hyph_fr.dic
      src/hyphenDicts/hyph_la.dic: usr/local/Kobo/hyphenDicts/hyph_la.dic
      src/otros/hosts-363: etc/hosts
    #  src/jcn-t.css: mnt/onboard/.kobo/kepub/kepub-book.css
      src/otros/jcn-t.css: mnt/onboard/kobo_extra.css
    #  src/translations/trans_es.qm: usr/local/Kobo/translations/trans_es.qm
      src/fonts/IBMPlexMono-Regular.ttf: mnt/onboard/fonts/IBMPlexMono-Regular.ttf
      src/fonts/IBMPlexMono-Italic.ttf: mnt/onboard/fonts/IBMPlexMono-Italic.ttf
      src/fonts/IBMPlexMono-Bold.ttf: mnt/onboard/fonts/IBMPlexMono-Bold.ttf
      src/fonts/IBMPlexMono-BoldItalic.ttf: mnt/onboard/fonts/IBMPlexMono-BoldItalic.ttf
    # https://sansforgetica.rmit/
      src/fonts/SansForgetica-Regular.otf:
        - mnt/onboard/fonts/SansForgetica-Regular.otf
        - mnt/onboard/.adds/koreader/fonts/Forgetica/SansForgetica-Regular.otf

**NOTE**: the last file (_SansForgetica-Regular.otf_) is copied twice.
