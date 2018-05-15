# Keyboard

All layouts can be found in `/usr/share/X11/xkb/symbols/`.

My command to set the layout is: `setxkbmap -model "pc105" -layout "us,ch,ch" -variant ",fr,fr_dev" -option "grp:alt_shift_toggle"`.

## The fr_dev variant
I made this variant because on french keyboard, the default behavior is to wait another
keypress for some symbols. Some of those symboles are widely used in developement. So this
is my variant:

```
partial alphanumeric_keys¬                                                                                                                                                                         
xkb_symbols "fr_dev" {¬                                                                                                                                                                            
    // modify the basic Swiss French layout to be more convenient for development¬                                                                                                                 
    include "ch(fr)"¬                                                                                                                                                                              
    name[Group1]= "French (Switzerland, optimized for developement)";¬                                                                                                                             
    key <AE11> { [  apostrophe, question, dead_acute   ] };¬                                                                                                                                       
    key <AE12> { [ asciicircum,    grave, asciitilde   ] };¬                                                                                                                                       
    key <AD12> { [  diaeresis,    exclam, bracketright ] };¬                                                                                                                                       
};
```
