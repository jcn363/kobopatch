Gutting kobopatch.yaml
=================================================

· Since the system used in Kobo is Linux, the UNIX nomenclature is used (with / instead of Windows \ ).

· Indents / spaces (as long as they are not in a comment) matter.

· Everything that follows the symbol # is a comment.

    ## Works with kobopatch v0.7 or higher.
    ## You can update kobopatch by downloading the latest release from https://github.com/geek1011/kobopatch/releases.

· Words that precede two points, and carry text below, are variables, which hold the value that follows them. Like a dictionary, word and definition.

    version: 4.10.11655
    in: src/kobo-update-4.10.11655.zip
    out: out/KoboRoot.tgz
    log: out/log.txt
    
    patchFormat: kobopatch

· _Version_: it is the version of the firmware that we want to patch -> the version.

· _In_: compressed (with its location) where the firmware we want to patch is located -> the firmware.

· _Out_: compressed (with your location) where the patched firmware will be saved ready for your application -> the patch.

· _Log_: information / reference file where orders, errors, etc. are stored gernerated during the creation of the patch -> the log.

· _PatchFormat_: is the format in which the patches are written -> kobopatch is the current one.

· Words that precede two points, but DO NOT carry text below, call a section.

· In the patches section we find a list of relationships

    file with patches: file to patch

· In a file with patches there can only be patches that modify a program or library. But there may be several files that modify the same program or library.

· Both libnickel-PROGRESIVE.yaml and libnickel.so.1.0.0.yaml contain patches that modify the same library: libnickel.so.1.0.0.

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

· In the overrides section there is a list of the files with patches (listed in the patches section) together with the patches they contain and if we want to apply them or not.

   The value assigned here overwrites the one that is inside the patch file (default is not).
   
   It is not mandatory, but it is very useful and convenient, since at a glance it allows us to see the patches that we have available along with their status. In addition it allows us to activate and deactivate patches (of different programs and libraries) without having to open the file that contains them to look for them in it.
   
   To say that we want to apply a patch called PROGRESIVE, which is inside the nickel-PROGRESIVE.yaml file, about the nickel program, regardless of whether it is activated or not, we would have to put:
   
    overrides:
      src/nickel-PROGRESIVE.yaml:
        PROGRESIVE: yes

**WARNING**: following this example the previous lines would not do anything, since in the patches section the line relative to nickel-PROGRESIVE.yaml was commented and therefore the program does not know what program it has to patch.
patches:

    #  src/nickel-PROGRESIVE.yaml: usr/local/Kobo/nickel

· There may be as many patches as there are patches within that file. As indicated, it is not mandatory that they are all, although very useful.

· The name of the patch must correspond exactly with the one that is inside the corresponding patch file.

· Remember that the value that prevails is the one indicated here.

    src/libnickel-PROGRESIVE.yaml:
      Dictionary, remove long winded closest match notification: yes
      unclassified (PROGRESIVE): yes
      MY ePub/Kepub fixed font sharpness: yes
      Wikipedia Search language: yes
      MY KePub stylesheet additions: yes
      My Dictionary text font-family/font-size/line-height: yes

· There are patches that are self-explanatory. If more than one is activated, at the same time, the program will stop, the patch will not be generated and we will have to resort to the log to know where is the error that we have committed.

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

· If you want to translate the messages of the system that appear on the screen (let's say for example), or simply correct some of them (they have sometimes put libri instead of books), you can do it. To do this, you must install the program lrelease or tell the patch program where you are using

    # lrelease: /path/to/lrelease

**NOTE**: I do not use it (it is commented) because I have it integrated in the system.

· In the translations section you must indicate the list of translations you want to include in the patch.

    file with your translation: file to include in the patch

    translations:
      src/translations/trans_es.ts: usr/local/Kobo/translations/trans_es.qm

· You may not want to translate but you have a file that you want to incorporate / replace the system (fonts, script files, etc.). That's what the files section is for. Remember: "_a place for everything and everything in its place_".

    file that you want to add to the patch: file with the location it will occupy in the device
    
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
