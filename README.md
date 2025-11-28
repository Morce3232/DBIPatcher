# DBI English Translation

This repository contains second iteration of an English translation for the DBI homebrew application (version 814-845) 
for Nintendo Switch.

After initial release Duckbill got rid of translation payload and instead opted for compile time string obfuscation,
using the mighty XOR encryption again.

This time, I really don't see myself updating this in the forseeable future. Because of that, I wanted to wait a bit 
longer before release, however version 846 changed (probably unintentionaly, as a side effect of refactoring) key
generator and updating current toolkit to support that would take more time than I am willing to invest.

For this reason I am releasing my work in its current state, which should come in handy, as it brings (at least 
preliminary) oficial HOS 21 support.

Everything should be hopefuly clear from code, which is (again) a mess, but should work.

## Important Disclaimers

### Author Controversy
This translation is provided independently and is not affiliated with or endorsed by the original DBI author. Users should be aware of ongoing community discussions regarding the original software and make informed decisions about its use.

**Do not forget about the warnings and threats from the author of the Russian version, and use these translations at your own risk:**

#### Author Warning on Independent Translations

> *04 Jul 2023*
> 
> **Nobody:** Why do you keep scaring people who use independent translations? Some poor Brazilians on Telegram even went full panik!
> 
> **Duckbill:** I like boys.

### Backup Your Console
**Before using any homebrew software, create a complete backup of your console:**
- NAND backup
- Console keys (prod.keys, title.keys)
- SD card contents

Store these backups in multiple secure locations. Console bricks can and do happen.

### No Warranties
This translation is provided as-is with no guarantees whatsoever. The author of this translation accepts no responsibility for any damage, data loss, console bricks, account bans, or other issues that may arise from using this modified software. Use at your own risk.

### Maintenance Notice
This repository is not actively maintained. No future updates are currently planned. The community is free to fork, modify, and distribute this work as needed.

## Technical Notes

I have included all relevant files I used for translation (nros, blueprints), HOWEVER: there is *slight* 
chance I migth have unintentionaly broken some of them. If in doubt, source your own.

Also might have unintentionaly broken something during cleanup - however version 845 was translated using final
version of this toolkit and seems to work fine.

This works on my xubuntu machine. Probably wont work on windows.

### Supported versions
This project supports all russian DBI versions after removal of translation blob (that would probably be &gt; 810).
Last supported version is 845. Support for further versions could be theoreticaly added, unless there will be
countermeasures taken after this going public.

### Testing status
Some of the versions were tested by few selected people, who were regularly using them without any issues. **Big thanks to everyone involved!**

Other versions were just casualy tested for obvious issues.

### Translation Method
Known strings from version 810 were used as basis for translation.

Strings are compile-time xor obfuscated.

It basically boils down to compiler optimising strings to one of the following types:

1. **>= 16B** - full strings stored in read only section, null terminated, 8 byte aligned
2. **> 8B and < 16B** - first 8 bytes stored in read only section, rest are instruction immediate values
3. **<= 8B** - instruction immediates only

Where only real issue are **2** and **3**, requiring instruction parsing. This currently appears to work well, however it
is still best to avoid using immediates where possible (prefer shorter strings).

String lookup takes some time, so application uses something I called **blueprints**, which contain locations to be 
patched as well as string ids going into those positions.

### Code Quality
This is experimental software. Code smells. But it works. :)


## Usage

### Quick Start
```bash
git clone <repository-url>
cd <repository-directory>
make
```

### Manual Usage
List of supported operations can be displayed using:

```
Usage: ./bin/dbipatcher --help
```

There is quick description of individual operations:

| Operation       | Description                                                                  |
|-----------------|------------------------------------------------------------------------------|
| **--find-imm**  | Searches instruction immediates for needle                                   |
| **--find-str**  | Searches read only data for needle                                           |
| **--find-keys** | Searches for XOR key candidates                                              |
| **--new-en**    | Searches for english string candidates not present in dictionary             |
| **--new-ru**    | Searches for russian string candidates not present in dictionary             |
| **--partials**  | Searches for instruction immediate portion of type 2 strings from dictionary |
| **--decode**    | Tries to decode string starting at given address                             |
| **--merge**     | Merges existing language file with dictionary. Performs various checks.      |
| **--scan**      | Used to create blueprints                                                    |
| **--patch**     | Patches nro using language file and blueprint                                |

Real workflow for patching theoretical new version is:

1. Find missing strings using **--new-en** and **--new-ru**, manualy add those to dictionary
2. If some of those appear to be of type 2, use **--partials** to find possible candidates, manualy update disctionary
3. **--merge** your language file with updated dictionary, translate missing keys
4. **--merge** your language file again, open in text editor and check for possible issues at its end
5. Create blueprint using **--scan** - you might want to check some of the locations in ghidra to verify they were correctly detected
6. **--patch** russian nro into english
7. Test patched file. if there are still some russian strings present, use **--find-str** or **--find-imm** to locate them, add to dictionary and repeat

## Legal Notice

This translation is distributed for educational and interoperability purposes. Users are responsible for complying with applicable laws and terms of service in their jurisdiction.

## License

This translation work is released into the public domain. The original DBI software remains under its original license terms.

## Honorable mentions

* Yorunokyujitsu and bohdan.buinich for independently figuring out the translations as well
* All the great people testing the translated builds
