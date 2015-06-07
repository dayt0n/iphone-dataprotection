  * **UID key** : hardware key embedded in the application processor AES engine, unique for each device. This key can be used but not read by the CPU. Can be used from bootloader and kernel mode. Can also be used from userland by patching [IOAESAccelerator](IOAESAccelerator.md). As stated in the [Apple iOS Security document](http://images.apple.com/iphone/business/docs/iOS_Security_Oct12.pdf) :
    * "The device's unique ID (UID) and a device group ID (GID) are AES 256-bit keys fused into the application processor during manufacturing."
    * "No software or firmware can read them directly; they can see only the results of encryption or decryption operations performed using them"
    * "The UID is unique to each device and is not recorded by Apple or any of its suppliers."
    * "The UID is not related to any other identifier on the device."
    * "A 256-bit AES key that's burned into each processor at manufacture."
    * "It cannot be read by firmware or software, and is used only by the processor's hardware AES engine."
    * "To obtain the actual key, an attacker would have to mount a highly sophisticated and expensive physical attack against the processor's silicon."
    * "The UID is not related to any other identifier on the device including, but not limited to, the UDID."

  * **UIDPlus key** : new hardware key referenced by the iOS 5 kernel, not used on iPhone 4S, used starting with the iPad 2 ?

  * **Key 0x835** :  Computed at boot time by the kernel. Only used for keychain encryption in iOS 3 and below. Used as "device key" that protects class keys in iOS 4.
    * `key835 = AES(UID, bytes("01010101010101010101010101010101"))`

  * **Key 0x89B** : Computed at boot time by the kernel. Used to encrypt the data partition key stored on Flash memory. Prevents reading the data partition key directly from the NAND chips.
    * `key89B = AES(UID, bytes("183e99676bb03c546fa468f51c0cbd49"))`

  * **EMF key** : Data partition encryption key. Also called "media key". Stored encrypted by key 0x89B

  * **DKey** : NSProtectionNone class key. Used to wrap file keys for "always accessible" files on the data partition in iOS 4. Stored wrapped by key 0x835

  * **BAG1 key** : System keybag payload key (+initialization vector). Stored unencrypted in effaceable area.

  * **Passcode key** : Computed from user passcode or escrow keybag BagKey using Apple custom derivation function (Tangling). Used to unwrap class keys from system/escrow keybags. Erased from memory as soon as the keybag keys are unwrapped.

  * **Filesystem key** (f65dae950e906c42b254cc58fc78eece) : used to encrypt the partition table and system partition (referred to as "NAND key" on the diagram)

  * **Metadata key** (92a742ab08c969bf006c9412d3cc79a5) : encrypts NAND metadata (vfl/ftl contexts & index pages)

# Keys hierarchy (iOS 4) #
![http://wiki.iphone-dataprotection.googlecode.com/hg/ios4_encryption_hierarchy.svg](http://wiki.iphone-dataprotection.googlecode.com/hg/ios4_encryption_hierarchy.svg)