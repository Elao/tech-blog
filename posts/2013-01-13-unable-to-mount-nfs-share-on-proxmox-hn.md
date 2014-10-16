
Hi guys just a little reminder about Proxmox and NFS share.  
I encountered a little issue when I was setting up a proxmox server with a NAS (for medias files).

I was able to mount the NAS from the Hardware Node, but noway to do it at boot, which can be problematic in case of untimely restart.  
I tried to used <a title="Autofs" href="http://www.autofs.org/" target="_blank">autofs</a> but I was not satisfied with this solution.

So I had a look on the mount manual and found a very interesting option called <a title="_netdev" href="http://linux.about.com/od/commands/l/blcmdl8_mount.htm" target="_blank">_netdev</a>.

To have a good understanding of this issue, the filesystem resides on a device that requires network access. **_netdev** is used to prevent the system from attempting to mount these filesystems until the network has been enabled on it.

<span style="text-decoration: underline;">My /etc/fstab entry will look as the following:</span>

<div class="codecolorer-container bash vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="bash codecolorer">
    XXX.XXX.XXX.XXX:<span class="sy0">/</span>nas-0045392<span class="sy0">/</span>medias <span class="sy0">/</span>mnt<span class="sy0">/</span>medias nfs user,auto,noexec,intr,noatime,_netdev <span class="nu0"></span> <span class="nu0"></span>
  </div>
</div>