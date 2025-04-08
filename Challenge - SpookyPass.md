Arrel de veure el video d'en S4vitar fer aquest challenge, m'he animat a fer-lo jo també. Primer de tot cal dir que és un challenge de reversing. El reversing és el procés d'analitzar un programari, sistema o codi per entendre el seu funcionament intern sense tenir accés al seu codi font. S'utilitza en diversos àmbits com:

-Anàlisi de malware: Per entendre com funciona un virus o troià i trobar maneres de detectar-lo o eliminar-lo.

-Auditoria de seguretat: Per descobrir vulnerabilitats en aplicacions o sistemes.

-Enginyeria inversa d'APIs: Per comprendre com interactuen les aplicacions i trobar possibles explotacions.

-Recuperació de codi: Quan el codi font original s’ha perdut o no es té accés a ell.


Aquest challenge per tant, no requereix connexió i el primer que hem de fer és descarregar els fitxers que ens dona HTB:

![image](https://github.com/user-attachments/assets/93888224-e858-46ac-b2bb-987114811948)

Un cop descarregats els descomprimim al directori on treballarem, i veiem que hi ha el ftixer anomenat pass a dins:

![image](https://github.com/user-attachments/assets/07f1add5-34fd-4ab9-8ec9-59e614c43bf5)

Ara, per saber el tipus de fitxer que és, li farem un file, comanda molt útil en linux:
````
┌──(kali㉿kali)-[~/Documents/SpookyPass/rev_spookypass]
└─$ file pass                                                                                       
pass: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=3008217772cc2426c643d69b80a96c715490dd91, for GNU/Linux 4.4.0, not stripped
````

I amb això veiem que és un binari: ELF 64-bit → És un binari en format ELF (Executable and Linkable Format) de 64 bits.

Més info sobre el fitxer:

-LSB pie executable → És un Position Independent Executable (PIE), cosa que permet que el carregador del sistema operatiu pugui carregar-lo en diferents adreces de memòria (millora la seguretat).

-x86-64 → Compatible amb arquitectures AMD64 / Intel 64.

-version 1 (SYSV) → Fa servir convencions System V.

-dynamically linked → Està enllaçat dinàmicament i necessita llibreries externes per executar-se.

-interpreter /lib64/ld-linux-x86-64.so.2 → El carregador dinàmic que s'utilitza per executar el binari.

-BuildID[sha1]=3008217772cc2426c643d69b80a96c715490dd91 → Identificador únic del binari basat en SHA1.

-for GNU/Linux 4.4.0 → Compilat per a un sistema basat en el kernel Linux 4.4.0 o posterior.

-not stripped → Conté informació de depuració (funcions, símbols, etc.), cosa que facilita l'anàlisi amb eines com gdb o objdump.

Per tant, el que tocarà fer és executar el fitxer:
````
┌──(kali㉿kali)-[~/Documents/SpookyPass/rev_spookypass]
└─$ ./pass                                                           
Welcome to the SPOOKIEST party of the year.
Before we let you in, you'll need to give us the password: 
````

I veiem que se'ns demana una contrasenya que no tenim. Al ser la categoria del challenge de HTB Reversing, haurem de veure com li podem fer reverse per obtenir aquesta contrasenya, però primer de tot, provarem de posar una contrasenya random a veure què passa:

````
┌──(kali㉿kali)-[~/Documents/SpookyPass/rev_spookypass]
└─$ ./pass
Welcome to the SPOOKIEST party of the year.
Before we let you in, you'll need to give us the password: hola
You're not a real ghost; clear off!
                                                                                                                                                                   
┌──(kali㉿kali)-[~/Documents/SpookyPass/rev_spookypass]
└─$ 
````

Es tanca i ens fa fora de l'executable. Per tant, ara provarem amb la comanda strings i llistarem les cadenes de text del binari que mostrarà totes les seqüències de caràcters imprimibles (normalment de 4 o més caràcters) dins del fitxer, a veure si veiem alguna cosa interessant:
````
┌──(kali㉿kali)-[~/Documents/SpookyPass/rev_spookypass]
└─$ strings pass    
/lib64/ld-linux-x86-64.so.2
fgets
stdin
puts
__stack_chk_fail
__libc_start_main
__cxa_finalize
strchr
printf
strcmp
libc.so.6
GLIBC_2.4
GLIBC_2.2.5
GLIBC_2.34
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
PTE1
u3UH
Welcome to the 
[1;3mSPOOKIEST
[0m party of the year.
Before we let you in, you'll need to give us the password: 
s3cr3t_p455_f0r_gh05t5_4nd_gh0ul5
Welcome inside!
You're not a real ghost; clear off!
;*3$"
GCC: (GNU) 14.2.1 20240805
GCC: (GNU) 14.2.1 20240910
main.c
_DYNAMIC
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_start_main@GLIBC_2.34
_ITM_deregisterTMCloneTable
puts@GLIBC_2.2.5
stdin@GLIBC_2.2.5
_edata
_fini
__stack_chk_fail@GLIBC_2.4
strchr@GLIBC_2.2.5
printf@GLIBC_2.2.5
parts
fgets@GLIBC_2.2.5
__data_start
strcmp@GLIBC_2.2.5
__gmon_start__
__dso_handle
_IO_stdin_used
_end
__bss_start
main
__TMC_END__
_ITM_registerTMCloneTable
__cxa_finalize@GLIBC_2.2.5
_init
.symtab
.strtab
.shstrtab
.interp
.note.gnu.property
.note.gnu.build-id
.note.ABI-tag
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.dynamic
.got
.got.plt
.data
.bss
.comment
````

Aquí trobem una cosa hiper interessant! Sembla que a sota de l'string que ens apareix al executar el programa i que demana la contrassenya, hem trobat el que podria ser la contrasenya: s3cr3t_p455_f0r_gh05t5_4nd_gh0ul5 .

Tornem a executar el binari i hi posarem aquesta contrasenya:

````
┌──(kali㉿kali)-[~/Documents/SpookyPass/rev_spookypass]
└─$ ./pass                                                 
Welcome to the SPOOKIEST party of the year.
Before we let you in, you'll need to give us the password: s3cr3t_p455_f0r_gh05t5_4nd_gh0ul5
Welcome inside!
HTB{un0bfu5c4t3d_5tr1ng5}
                                                                                                                                                                   
┌──(kali㉿kali)-[~/Documents/SpookyPass/rev_spookypass]
└─$ 
````

Hem tingut èxit i hem obtingut la flag del challenge!! Ara la posem a HTB i ja tindrem el challenge complert!

![image](https://github.com/user-attachments/assets/aef6fc7a-a919-4f22-9872-bb5452312bf1)
