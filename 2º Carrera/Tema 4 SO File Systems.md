I'm going to provide a brief review of some of the most common File Systems. These are the following:
- NTFS
- ext4
- APFS

### New Technology File System (NTFS).-

Starting off with NTFS, it is a proprietary journaling file system developed by Microsoft. Starting with Windows NT 3.1, it is the default system of the Windows NT family. It replaced FAT (File Allocation Table) as the preferred FS on windows, supported in Linux and BSD as well. NTFS read and write support is provided using a free open-source kernel implementation known as NTFS3 in Linux and NTFS-3G driver in BSD. In windows, you can convert FAT32 16 and 12 into NTFS without having to rewrite all files using the `convert` command.
NTFS uses many hidden files to store metadata about other files stored on the drive which helps improving speed and performance when reading data.
Unlike FAT and HPFS, NTFS supports ACLs, system encryption, transparent compression, sparse files and file system journaling. NTFS also suports shadow copy to allow backups of a system while it is running, varying its functionality between different versions of Windows.

#### History.-

In the mid 1980s, Microsoft and IBM formed a joint project looking forward to creating the next gen of graphical OS, the result was OS/2 and HPFS. Because of disagreement between the two of them, they separated, with OS/2 remaining as an IBM project. Microsoft worked to develop Windows NT and NTFS.
The HPFS for OS/2 had several important new features. When Microsoft created their OS, they borrowed many concepts for NTFS.
HPFS and NTFS use the same disk partition identification type code (07). This is highly unusual, as there were dozens of unused numbers available. As a result of this, any algorithm that detects type 07 must perform additional checks to distinguish between HPFS and NTFS.

#### Scalability.-

NTFS is optimized for 4KB clusters, but with a maximum cluster size of 2MB.

#### Interoperability.-

###### Windows.-
While the different NTFS versions are mostly forward- and backward-compatible, there are considerations taking place when mounting newer NTFS volumes in older versions of Microsoft Windows. Affecting dual booting and external portable hard drives. As an example, attempting to use an NTFS partition with previous versions on an OS that doesn't support it will result in the contents being lost.

###### FreeBSD.-
It included read-only NTFS support. The FreeBSD implementation of NTFS was ported to OpenBSD and offers native read-only NTFS support by default on i386 and amd64 platforms.

###### Linux.-
Linux kernel versions 2.1.74 and above include a driver which has the ability to read NTFS partitions. Versions 2.5.11 and above contain a new driver which supports file read, the ability to write to files got introduced in 2.6.15, which allowed users to write to existing files but doesn't allow the creation of new ones. Paragon's NTFS driver merged into kernel version 5.15, supports  read/write on normal, compressed and sparse files, as well as journal replaying.

###### MacOS.-

Mac Os X 10.3 included Ustimenko's read-only implementation of NTFS from FreeBSD. Native NTFS write support is included in 10.6 and later, but not activated by default.

###### OS/2.-
 The NetDrive package for OS/2 and derivates supports a plugin which allows read and write access to NTFS volumes.
###### DOS.-
There is a free-for-personal use read/write driver for MS-DOS called NTFS4DOS.

#### Security.-

NTFS uses access control list and user-level encryption to help secure user data.
###### Access Control Lists (ACLs).-
In NTFS, each file or folder is assigned a security descriptor that defines its owner and contains two access control lists. The first ACL (DACL) defines exactly the type of interaction allowed or forbidden by which user or group of users.
The second ACL (SACL) defines which interactions with the file or folder are to be audited and whether they should be logged when the activity is successful, failed or both.

###### Encryption.-
EFS provides user-transparent encryption of any file or folder on an NTFS volume. It works by encrypting a file with a bulk symmetric key (FEK), which is used because it takes a small amount of time to encrypt and decrypt large amounts of data compared to an asymmetric key cipher. The symmetric key used to encrypt the file is then encrypted with a public key associated with the user who encrypted the file. The process is then done backwards. There is support of additional decryption keys built into EFS, in the case of a user losing access to their key.

#### Features.-

###### Journaling.-
NTFS is a journaling FS and uses the NTFS Log to record metadata changes to the volume, feature that FAT doesn't provide, which is critical for NTFS to ensure that the complex internal data structures remain consistent if crashes or data moves by the defragmentation API were to occur. It allows easy rollbacks of uncommitted changes to these critical data structures when the volume is remounted.

###### Hard Links.-
The hard link feature allows different file names to directly refer to the same file contents. Hard links may link only to files in the same volume, because each volume has its own MFT. Hard links were originally included to support the POSIX subsystem in Windows NT.
Meanwhile hard links use the same inode, which records file metadata such as file size, modification date and attributes, NTFS also caches this data in the directory entry as performance enhancement.

###### Alternate Data Stream (ADS).-
ADS allow more than one data stream to be associated with a filename (forks).
NTFS streams were introduced in windows NT 3.1, to enable services for Macintosh (SFM) to store resource forks. third-party Apple Filing Protocol (AFP) products use this feature on the FS, but current versions of WIndows Server don't.
Using alternate streams from critical data could cause problems. Starting with Windows PowerShell, it is possible to manage ADS natively.
Malware has used ADS to hide code, making malware scanners and different tools check for alternate data streams now.

###### File Compression.-
Compression is enabled on a per-folder or per-file basis, by setting the *compressed* attribute. When any files are moved to / saved on that folder, LZNT1 algorithm automatically compresses those. NTFS compression is only available for cluster sizes up to 4 KB. If the data compressed reduces 64 KB to 60 KB or less, the unneeded 4 KB are treated as empty sparse file clusters, thus they are not written, allowing for reasonable random-access times.
Compression also works bets with files that include repetitive content.

- Advantages:
Users of fast multi-core processors will find improvements in app speed by compressing their applications and data, as well as a reduction in space used, even when SSD already compress data, there is an upgraded improvement.

- Disadvantages:
Large compressible files become highly fragmented since every chunk smaller than 64 KB becomes a fragment. SSD drives don't have the head movement delays and high access time of hard disk drives, so fragmentation has onlly a smaller penalty.
If system files needed at boot time are compressed, the system could fail to boot correctly, as decompression filters are not yet loaded.

###### System Compression.-
Since Windows 10, Microsoft has introduced new file compression scheme based on the XPRESS algorithm with 4K/8K/16K block size and the LZX algorithm.
The new compression scheme is used by CompactOS feature, which reduces disk usage by compression Windows system files. CompactOS is not an extension of NTFS file compression, and doesn't use the *compressed* attribute.
What it does instead is setting a reparse point on each compressed file with a WOF (Windows Overlay Filter), but the data is stored in an ADS called "WofCompressedData", which is decompressed by a WOF filesystem filter driver, and the main file is an empty sparse file. This design is merely for read-only access, meaning any write would decompress the file.

###### Sparse Files.-
Sparse files are files interspersed with empty segments for which no actual storage space is used. To the applications, the file looks like an ordinary file with empty regions with zeros. The FS maintains an internal list of said regions for each sparse file. A sparse file does not necessarily include sparse zeros areas. The attribute *sparse* means that they are allowed to have those spaces.

###### Volume Shadow Copy.-
The VSS keeps historical version of files and folders on NTFS volumes by copying old, newly overwritten data or shadow copy via copy-on-write technique. It allows data backup programs to archive files in use by the file system.
Windows Vista introduced persistent shadow copies for use with System Restore. The only problem is that older OS delete said copies, as they don't understand that format.

###### Transactions.-
Apps can use Transactional NTFS to group changes to files together into a single transaction, guaranteeing every change will happen, or none happen. No app will see the changes until they are done.
With a similar technique as the one for VSS, so that overwritten data can be safely rolled back. It also has a CLFS log to mark uncommitted transactions, or committed but not fully applied.
TxF is strongly advised to not be used, instead utilizing alternative options, as further Windows versions won't use Transactional NTFS.

###### Quotas.-
Disk quotas allow the admin of a NTFS supporting PC to set a threshold of disk space available for users to utilize. Alllowing the admins to keep track of how much disk space each user is using. Having the ability to specify a maximum of disk space to be used for each user before they get a warning, and then access denied when the limit is reached.

###### Reparse points.-
Reparse points are used by associating a reparse tag in the user space attribute of a file or directory. When the Object Manager parses a file system name lookup and encounters a reparse attribute, it will reparse the name lookup, passing the user controlled reparse data to every file system filter driver loaded into Windows to check it it matches.

#### Limitations.-

###### Resizing.-
Microsoft added the built-in ability to shrink or expand a partition, but this doesn't relocate page file fragments or files marked as unmovable, so shrinking a volume will often require relocating page files.

###### OneDrive.-
Since 2017, the OneFile structure has to reside on an NTFS disk. Because of OneDrive "Files On-Demand" feature uses NTFS reparse points to link files and folders that are stored on OneDrive to the local file system. This makes the file or folder unusable with previous Windows versions with other NTFS file system driver.

#### Structure.-

NTFS is made up of several components including:
-partition boot sector (PBS).
-master file table.
-series of meta files.
-data streams and locking mechanism.

NTFS uses B-trees to index file system data. A file system journal is used to guarantee the integrity of the file system metadata, but not individual files content. Systems using NTFS are known to have improved reliability compared to FAT file systems.
UTF-16 code units are supported, but the file system doesn't check if it is valid.

###### Partition Boot Sector.-
It is roughly based upon the earlier FAT file system, but the fields have different locations. The OS first looks at the 8 bytes at 0x30 to find the cluster number of $MFT, then multiplies it by the number of sectors per cluster (byte at 0x0D). This value is the sector offset to the $MFT.

###### Master File Table.-
In NTFS, every file, directory and metafile data, are stored in the MFT. This abstract approach allows easy addition of file system features during Windows NT development.
The MFT structure supports algorithms which minimize disk fragmentation. In case of corruption, a partial copy of the MFT, called MFT mirror is stored. Both MFT and its mirror are stored in the boot sector.

###### Metafiles.-
NTFS contains several files that define and organize the file system. The metafiles define files, back up critical file system data, buffer file system changes, manage free space allocation, satisfy BIOS expectations, track bad allocation units and stores security and disk space usage information. All content is in an unnamed data stream, unless otherwise indicated.

###### Attribute lists, attributes and streams.-
For each file or directory in the MFT record, there is a linear repository of attributes packed together in one or more MFT records. Each attribute has a type (an integer).
For each file in the MFT, the attributes identified by attribute type and name must be unique.
Some attributes are mandatory, and if they're null, its an unused record. Standard attributes must remain unnamed. The default data stream of a regular file is a stream of type $DATA with an anonymous name. 

###### Resident vs. non-resident attributes.-
To optimize storage and reduce I/O overhead for the common case of attributes with very small associated value. NTFS prefers to place the value within the attribute itself, instead of using the MFT record space to list clusters with the data, it will store an allocation map pointing to the data. Resident data is when the value can be accessed directly from within the attribute.
Some attributes can't be made non-resident. For those attributes, their allocation map must fit within MFT records. Encrypted-by-NTFS, sparse data streams or compressed data streams cannot be made resident. When there are too many attributes for a file, so that they cannot fit all within the MFT record, extension records may also be used to store the other attributes, using the same format.
x
###### Opportunistic locks.-
Oplocks allow clients to alter their buffering strategy for a given file or stream in order to increase performance and reduce network use. They apply to the given open stream of a file and don't affect oplocks on a different stream.
Oplocks can be used to transparently access files in the background. A network client may avoid writing information into a file on a remote server if no other process is accessing the data.
Windows support four different type of oplocks:
- Level 2 oplock: multiple readers, no writers.
- Level 1 oplock: exclusive access with arbitrary buffering.
- Batch oplock: a stream is opened on the server, but closed on the client machine.
- Filter oplock: applications and file system filters can "back out" when others try to access the same stream

#### Time.-
Windows NT and its descendants keep internal timestamps as UTC and make the appropriate conversions for display purposes.
The versions of windows that don't support NTFS all keep time as local zone time, and so do all file systems appart from NTFS that are supported by newer versions of windows. If files from NTFS to non NTFS partitions are moved, the OS converts the timestamps, but it can result in ambiguities, mostly if a local zone time changed recently, giving up to 4 hours of timestamp error.



---
## Fourth Extended Filesystem (ext4).-

ext4 is a journaling file system for LInux, successor of ext3. It was initially a series of bakward-compatible extensions of ext3, developed by Cluster File Systems mostly.
It is the default File System for Debian and Ubuntu.

#### Features.-
###### Large File System.-
ext4 can support up to 64 ZiB (zebibytes) of volume and single files with sizes up to 16 TiB (tebibytes) with standart 4 KiB block size and volumes with sizes up to 1 YiB (yobibyte) and 64 KiB clusters. A limitation in the extent format makes 1 EiB (Exibibyte) the practical limit. Everything grows proportionately with the FS block size up to max 64 KiB block size.

###### Extents.-
This replaces the traditional block mapping used by ext2 and ext3. An extent is a range of contiguous physical blocks, improving large file performance and reducing fragmentation, a single extent in ext4 can map up to 128 MiB of contiguous space with a 4 KiB block size. There can be four extents stored directly in the inode; if more, they get indexed in a tree.

###### Backward Compatibility.-
ex4 is backward-compatible with ext3 and ext2, making it possible to mount ext3 and ext2 as ext4, slightly improving performance, as new features of ext4 can also be used with ext3 and ext2. Features such as the new block allocation algorithm, without affection the on-disk format. ext3 is also partially forward-compatible with ext4.

###### Persistent Pre-allocation.-
ext4 can pre-allocate on-disk space for a file. To do this on most FS, zeroes would be written to the file when created. In ext4, `fallocate()` can be used. This allocated space would be guaranteed and most likely contiguous. This apply for media streaming and databuses.

###### Delayed allocation.-
ext4 uses a performance technique called allocate-on-flush, or delayed allocation. This technique delays block allocation until data is flushed to the disk. Delayed allocation improves performance and reduces fragmentation by effectively allocating larger amounts of data at a time.

###### Unlimited number of Subdirectories.-
ext4 doesn't limit the number of directories in a single directory, except in the inherent size limit of the directory itself. (ext3 can have up to 32k subdirectories)
To allow for larger directories and continued performance, ext4 has HTree indices by default, which allows directories up to 10-12 million entries to be stored in the 2-level HTree index.

###### Journal Checksums.-
ext4 uses checksums in the journal to improve reliability, since the journal is one of the most used files of the disk. This features benefits from being able to safely avoid a disk I/O wait during journaling, which improves performance.

###### Metadata Checksumming.-
in ext4 since Linux kernel 3.5 (2012).

###### Faster file-system checking.-
In ext4, unallocated block groups and sections of the inode table are marked as such. Enabling system utility to skip them entirely and reducing the file system check time.

###### Multiblock allocator.-
When ext3 appends to a file, it calls the block allocator, once for each block. This can propitiate fragmentation, but as ext4 uses delayed allocation, this is a really unlikely situation to happen.

###### Improved timestamps.-
ext4 introduces nanosecond-based timestamps to address the limitation of second-based timestamps as computers advance. Additionally, it tackles the year 2038 problem by incorporating 2 bits into the timestamp field. 
ext4 also supports creation timestamps, but implementing system calls like stat() and related libraries poses challenges, requiring coordination across multiple projects. Currently, the creation date in ext4 is only accessible to user programs on Linux through the `statx()` API.

###### Project quotas.-
Support for project quotas was added in Linux kernel 4.4. (2016) This feature allows assigning disk quota limits to a project ID. It can be assigned to a particular subdirectory tree independent of the file access permissions of the file, such as user and project quotas dependent on the UID and GID. It's not strictly hierarchical.

###### Transparent Encryption.-
Support for this feature was added in Linux kernel (2015).

###### Lazy initialization.-
This feature allows cleaning of inode tables in background, speeding initialization when creating new ext4 FS.

###### Write barriers.-
ext4 enables write barriers by default. It ensures that FS metadata is correctly written and ordered on disk. This has a performance cost, mostly for apps that use `fsync` heavily or create and delete many small files.

#### Limitations.-
ext4 does not support the "secure deletion" file attribute, intended for overwriting files upon deletion. Despite a 2011 patch proposal, the problem persisted, which didn't solve the problem of sensitive data in the file-system journal.

#### Delayed allocation and potential data loss.-
ext4 automatically handles some scenarios, as delayed allocation changes the behavior that programmers have been relying on in ext3, so i'ts automated to not risk potential data loss.
This could be the case of data loss:
`fd=open("file, O_TRUNC); write(fd, data); close(fd)`
But there's risk that the write doesn't succeed, processes access the file while the write, or some processes have already opened the file, causing them to crash as changes are done to it.
This is an alternate and safer way to do it:
`fd=open("file.new"); write(fd, data); close(fd); rename("file.new", "file");`
This way it guarantees the data is changed safely and prevent crashes, thus making it safer.
Still, the only way to be completely safe is to write and use `fsync()` when needed.

#### Implementation.-
Linux kernel VFS is a subsystem or layer inside the Linux kernel. A result of attempts to integrate multiple FS into a single structure, using the idea of abstract out a part of the FS that is common to all FS, and separate it from the rest of every FS code layer.

###### Compatibility with Windows and Macintosh.-
ext4, as of now, has full support on non-Linux OS. Microsoft can access ext4 since Windows 10, thanks to WSL, the windows subsystem for Linux (2016). ext2 to ext4 is available on Windows 7 SP1/8/8.1/10 thanks to Paragon (German company).
MacOS has full read/write capability for ext2 to ext4 through extFS by Paragon as well.

---

## Apple File System (APFS).-

APFS is a propietary FS developed by Apple Inc. for macOS Sierra, and later iOS 10.3, tvOS 10.2, watchOS 3.2 and all versions of iPadOS. It's aim is to fix core problems of HFS+ (MacOS extended), APFS is optimized for solid-state drive storage and supports encryption, snapshots and increased data integrity, among other capabilities.

#### History.-
APFS was announced at the WWDC in 2016, as a HFS+ replacement, which had been in use since 1998. It was released for 64 bit iOS on 2017, on pair with the release of iOS 10.3 and macOS 10.13 respectively.

#### Design.-
The FS can be used on devices with small or large amounts of storage. It uses 64 bit inode numbers and allows for more secure storage. It uses TRIM command for better space management and performance, just like HFS+.

###### Partition scheme.-
APFS uses GPT partition scheme. macOS Catalina introduced the APFS volume group, which are groups of volumes that Finder displays as one volume. APFS firmlinks lie between soft and hard links, and between volumes.

###### Clones.-
Clones allow the OS to make efficient file copies on the same volume without occupying additional storage space. Changes to a cloned file are saved as delta extents, reducing storage space. There's no interface to mark two files as copies of the other file.

###### Snapshots.-
APFS volumes support snapshots for creating a point-in-time, read-only instance of the FS.

###### Encryption.-
APFS natively supports full disk encryption, and file encryption with:
-No encryption.    -Single-key encryption.
-Multi-key encryption, where each file is encrypted with a separate key, just like it's metadata.

###### Increased maximum number of files.-
APFS supports 64-bit inode numbers, supporting over 9 quintillion files (2⁶³) on a single volume.

###### Data integrity.-
APFS uses checksums to ensure data integrity, but only for metadata.

###### Crash Protection.-
APFS is designed to avoid metadata corruption caused by system crashes. Instead of overwriting existing metadata records in place, it writes entirely new records, points to the new ones and release the old ones (redirect-on-write).

###### Compresion.-
APFS supports transparent compression on individual files using Deflate, LZvN and LZFSE, all of which are Lempel-Ziv -type algorithms.

###### Space sharing.-
APFS adds the ability to have multiple logical drives (volumes) in the same container where free space is available to all volumes in that container.

#### Limitations.-
While APFS includes numerous improvements relative to HFS+, a number of limitations have been noted.

###### Limited integrity checks for user data.-
APFS doesn't provide checksums for user data. It also doesn't take advantage of byte-addressable non-volatile RAM.

###### Performance on hard disk drives.-
Enumeration files, and any inode metadata in general, is much slower on APFS when it is located on a hard disk drive. APFS stores metadata alongside the file data. This creates a fragmentation acceptable for SSDs but not for HDDs.

###### Compatibility with Time Machine prior to macOS 11.-
Unlike HFS+, APFS doesn't support hard links to directories. Since the version of Time Machine backup in mac OS X 10.5, through macOS 10.15 relied on hard links to directories, APFS was initially not a supported option for its backup volumes. This limitation was overcomed in macOS 11 Big Sur, where APFS would become the default FS for the new Time Machine backups.

#### Security Issues.-
A bug of the APFS driver in High Sierra, which made disk encryption password to be logged in plaintext was found in 2018, and in 2021, the same driver in iOS < 14.4, macOS < 11.2, watchOS < 7.3 and tvOS < 14.4 versions had a bug that allowed local users to read arbitrary files, regardless of their permissions.

#### Support.-
###### macOS.-
An experimental version of APFS with some limitations is provided in macOS Sierra 10.12.4. It is available through the command `diskutil` utility. Limitations such as:
-Performing unicode normalization.
Since macOS 10.13 High Sierra, every tdevice with flash storage are automatically converted to APFS. Fusion drives and hard disk drives are upgraded on installation for macOS 10.14 Mojave an so on.

###### iOS, tvOS and watchOS.-
They convert the existing HFSX FS to APFS on compatible devices.

###### Third-party utilities.-
Despite how common APFS volumes are in today's Macs, and the 2016 format introduction. third party utilities continue to have notable limitiations in supporting APFS volumes, due to Apple's delayed release of the whole documentation.
There's currently work to create and integrate to Linux kernel an "APFS for Linux".

