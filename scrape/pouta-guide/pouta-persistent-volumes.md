Persistent volumes,  as the  name says, stay  even when  instances are
removed. They  can be  attached to  or detached  from virtual machines
while they are running.

### 4.2.1 Creating and attaching volumes with the Pouta web interface

Persistent volumes  can be created  using either the web  interface or
through the command line interface.

In  the  web  interface,  use  the **Create  volume**  button  on  the
*Volumes* page to create a new volume.  You may then give a name and a
size for the volume (1GB is  the minimum). The only mandatory argument
is the size of the volume.

![][1]

Once the  volume has  been created,  it can be  attached to  a running
virtual  machine. One  volume  can  be attached  to  only one  virtual
machine at a time.

To attach a  volume, select first the *Volumes* view  in the Pouta web
interface. Click the  arrow symbol next to the  **Edit Volume** button
for   the   volume   you    want   to   attach   and   select **Manage
attachments**. Select the instance (i.e.  virtual machine) you want to
attach the volume to in the **Attach To Instance** selector.

![][2]

### 4.2.2 Using attached volumes

The first time  you use a attached volume it  needs to be initialized.
**This should ONLY be done the  FIRST time you use it**, otherwise you
overwrite all your data on the volume. First determine which device is
your volume.

The following gives  a simple usage example for  creating a filesystem
on  a   volume  and mounting  the  filesystem   automatically after  a
reboot.  Note that  this  is a  simple  example and  there  are a  lot
of cooler ways to manage your filesystems.

Once  you have  logged into  your virtual  machine, you  can list  the
volumes with:

    sudo parted -l

You should be able to identify the  volume based on its size. For this
exercise,  let's say  that  it  is /dev/vdb.  First  we create  a<span
style="text-align:  justify;"> file  system on  it.  We  are going  to
use</span>* xfs*<span  style="text-align:  justify;"> because we  know
that it works well in Pouta:</span>

    sudo mkfs.xfs /dev/vdb

 

Now  you  can   start  using  it. For  example,  to   mount  it  under
/media/volume, you first need to make sure that the path exists:

    sudo mkdir -p /media/volume

Then you can mount it

    sudo mount /dev/vdb /media/volume

Remember to unmount the volume before detaching it!

    sudo umount /dev/vdb

If you  want the volume  to be  available after rebooting  the virtual
machine, you will need to add it to the /etc/fstab configuration file.
You can use the label that you previously created for the partition:

    sudo sh -c 'echo "/dev/vdb     /media/volume    xfs    defaults,nofail    0    2" >> /etc/fstab'

##### Subsequent additions

After you attach the volume to another machine, you only need to run:

    mkdir -p /media/volume
    mount /dev/vdb /media/volume

 

### 4.2.3 Creating and attaching volumes with command line tools

Persistent volumes can also be  created and attached using the command
line tools. The volume creation command is:

~~~~
openstack volume create --description "<description>" --size <size> <name>
~~~~

You can list existing volumes with the following command:

~~~~
openstack volume list
~~~~

You can  list existing virtual machines  to find the one  to which you
want to attach a volume to:

~~~~
openstack server list
~~~~

When a volume's status is "available",  you can attach it to a virtual
machine (you can  use either names or  ids to refer to the  VM and the
volume):

~~~~
openstack server add volume <virtual machine> <volume>
~~~~

<span style="line-height: 1.5;">A  volume can only be  attached to one
virtual machine at a time.</span>

When you  no longer  need the  volume to be  attached, you  can detach
it. **Before detaching, remember to unmount the volume's filesystem on
the virtual machine to avoid data loss! **The detach volume command is
as follows:

~~~~
openstack server remove volume <server> <volume>
~~~~

### 4.2.4 Transfering volumes between other Pouta projects

Occasionally,   you  may have  need  of  transferring your  persistent
volumes between Pouta projects. For  example, you may need to transfer
large data  sets or  bootable volumes to  colleagues in  another Pouta
project. This  can be done  using volume transfers.   Volume transfers
between  projects  in  Pouta  are fast,  avoid  data  duplication  and
unnecessary data transfers  over the network. Transfering  a volume to
another project  means that your project  no longer has access  to it.
Please note Pouta volume transfer  works within same cloud environment
i.e.  you can  transfer volume from one cPouta project  to another but
not between a cPouta project and ePouta project or vice versa.

For  transferring  volume,  you  must   first  make  sure  its  status
is **Available**.  You can  do so  by detaching  it from  the instance
where it was initially attached. Once  your volume is in the available
status, you  can initiate  volume transfer either  using Pouta  Web or
command line interface.

For Pouta Web interface,  go to *Volumes* view and  click arrow symbol
next to **Edit Volume** button of  the volume you want to transfer and
select **Create Transfer.**  Give a name to this  transfer request and
click on **Create Volume Transfer.** You will then get volume transfer
credentials (transfer ID & Authorization key).

![][3]

You need  to provide these credentials  to your colleague to  whom you
want to transfer this volume.

Your colleague can accept this Volume transfer in his project by going
to  his  *Volumes*  view  of   web  interface  and  clicking  **Accept
Transfer**  button.  He needs  to  then  provide transfer  credentials
generated by you in the  previous step and **Accept Volume Transfer.**
This will transfer the volume to your colleague's project.

![][4]

Volume transfers  can be also  done using command line  tools, Command
for creating a volume transfer is 

    openstack volume transfer request create <name or UUID of volume to transfer>

The  output  of this  command  will  have volume transfer  credentials
(transfer ID  & Authorization key),  note them  and pass them  to your
colleague to whom you want to transfer volume.

Your colleague  can accept transfer  request of this volume,  by using
the following command

    openstack volume transfer request accept <transferID> <authKey>

 

|                    | | | | |
|--------------------|-----|----------------|-----|----------------|
| [Previous chapter] |     | [One level up] |     | [Next chapter] |

  [1]: https://research.csc.fi/documents/48467/51198/create-volume-horizon.png/606e03f1-0d15-4243-aa4b-f4de9445a2a8?t=1474289809417
  [2]: https://research.csc.fi/documents/48467/51198/volume-attach-horizon2.png/2725549b-fb2b-48e3-935a-c8d72e2b0148?t=1474290373568
  [3]: https://research.csc.fi/documents/48467/115407/pouta-volume-transfer-creation.png/15c0df32-c29d-45cb-9a07-c1ed5a5be555?t=1531829251965
  [4]: https://research.csc.fi/documents/48467/115407/pouta-accept-volume-transfer.png/23a33f64-5388-4e4d-9a9c-7e3db4bbb1e9?t=1531829245195
  [Previous chapter]: https://research.csc.fi/pouta-ephemeral-storage
  [One level up]: https://research.csc.fi/pouta-user-guide
  [Next chapter]: https://research.csc.fi/pouta-snapshots
