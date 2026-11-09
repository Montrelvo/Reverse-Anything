# Exile installer findings for Idle Exiles

This static investigation used the four supplied Windows installers. The recovered WinHelp documents were treated as reference material. Installer and game executables stayed unlaunched.

| Installer | CRC-verified game payload entries | Primary game executable | Help inspected |
| --- | ---: | --- | --- |
| `EXILINST.EXE` | 33 | `EXILE.EXE` | `EXILE.HLP` |
| `EXL2INST.EXE` | 37 | `EXILE2.EXE` | `EXILE2.HLP` |
| `EXL3INST.EXE` | 50 | `EXILE3.EXE` | `EXILE3.HLP` |
| `BoEWinInstaller.EXE` | 68 | `BLADES.EXE` | `BLADHELP.HLP` |

The files identify the launcher as Setup Factory 16-Bit Setup Module 4.01. The embedded archive signature starts at file offset `0x46b0` in all four installers. Binary Refinery's Setup Factory reader recovered `irsetup.dat`. A focused parser handled this variant's variable-length `CFileInfo` records, read each compressed size and CRC from the file table, and decoded sequential PKWARE streams. Every payload entry matched the recorded CRC, and the final entry ended at EOF in each installer.

All four game executables have a 16-bit Windows NE header. Reverse Anything 6.5.0's static artifact command rejected `BLADES.EXE` as an unsupported NE image. The mechanics below are established by the shipped help and cross-checked against the public Blades source where listed. They are not runtime observations.

| Mechanic | Shipped help observation | Idle Exiles implementation |
| --- | --- | --- |
| Action economy | A base four action points is visible in Exile II at `0xddd4`, Exile III at `0x1549d`, and Blades at `0x13ddb`. The later documents describe turn end at zero or less. | Each NPC receives four AP in the automated vault encounter. A strike consumes four. |
| Spellcasting | Exile II and III describe a spell-point pool and distinct mage and priest commands. The Blades source charges six AP for mage spells and five for priest spells and clamps the AP result at zero. | Ivo attacks with Firebolt, Sable heals with Mend, and each cast spends two SP. |
| Parry | Exile I at `0xc925`, Exile II at `0xe739`, Exile III at `0x15e40`, and Blades at `0x1477b` describe spending all remaining AP to parry. | The NPC gives up remaining actions and halves incoming damage for the round. The damage multiplier is a prototype rule. |

The cited offsets identify printable strings in the extracted WinHelp files. Other text can be compressed or use phrase substitutions, so a string miss does not establish that a feature was absent.

The camp, scouting, gathering, outposts, upgrade costs, and ritual are original design. The first vault encounter is a deterministic NPC skirmish based on the documented combat rules.

- [Blades of Exile source at the inspected revision](https://codeberg.org/Montrelvo/Blades-Of-Exile-REA/src/commit/14ec69aaf463ca494ff022d88bfc6ee8638c6097)
- [Spiderweb Software's Blades source release](https://www.spiderwebsoftware.com/blades/opensource.html)
- [Idle Exiles](https://github.com/Montrelvo/Idle-Exiles)
