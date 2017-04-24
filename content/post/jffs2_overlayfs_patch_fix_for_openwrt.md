+++
date = "2017-04-24T20:12:03+08:00"
title = "Fix jffs2 overlayfs patch for OpenWrt and LEDE"

+++

Overlayfs have been implemented in linux kernel for some file systems, notably ubifs and ext4.
But for JFFS2, there's no such feature implemented in kernel yet.
The only implementation availble is a patch from OpenWRT and LEDE project.
After work with it, I found a bug.
To reproduce the issue, I run openwrt:master on a WR703N device, and follow below steps:

    #mount -t overlay overlay -o lowerdir=/,upperdir=/overlay/upper,workdir=/overlay/work /
    #rm -r /etc
    #rm -r /lib
    #dmesg
    [  421.340602] ------------[ cut here ]------------
    [  421.343925] WARNING: CPU: 0 PID: 1421 at fs/inode.c:273 drop_nlink+0x28/0x64()
    [  421.351275] Modules linked in: ath9k ath9k_common pppoe ppp_async iptable_nat ath9k_hw ath pppox ppp_generic nf_nat_ipv4 nf_conntrack_ipv6 nf_conntrack_ipv4 mac80211 ipt_REJECT ipt_MASQUE
    RADE ebtable_nat ebtable_filter ebtable_broute cfg80211 xt_time xt_tcpudp xt_state xt_nat xt_multiport xt_mark xt_mac xt_limit xt_conntrack xt_comment xt_TRACE xt_TCPMSS xt_REDIRECT xt_LOG x
    t_CT ums_usbat ums_sddr55 ums_sddr09 ums_karma ums_jumpshot ums_isd200 ums_freecom ums_datafab ums_cypress ums_alauda slhc nf_reject_ipv4 nf_nat_redirect nf_nat_masquerade_ipv4 nf_nat nf_log
    _ipv4 nf_defrag_ipv6 nf_defrag_ipv4 nf_conntrack_rtcache nf_conntrack iptable_raw iptable_mangle iptable_filter ip_tables ebtables ebt_vlan ebt_stp ebt_snat ebt_redirect ebt_pkttype ebt_mark
    _m ebt_mark ebt_limit ebt_ip ebt_dnat ebt_arpreply ebt_arp ebt_among ebt_802_3 crc_ccitt compat ledtrig_usbdev xt_set ip_set_list_set ip_set_hash_netiface ip_set_hash_netport ip_set_hash_net
    net ip_set_hash_net ip_set_hash_netportnet ip_set_hash_mac ip_set_hash_ipportnet ip_set_hash_ipportip ip_set_hash_ipport ip_set_hash_ipmark ip_set_hash_ip ip_set_bitmap_port ip_set_bitmap_ip
    mac ip_set_bitmap_ip ip_set nfnetlink ip6t_REJECT nf_reject_ipv6 nf_log_ipv6 nf_log_common ip6table_raw ip6table_mangle ip6table_filter ip6_tables x_tables usb_storage uhci_hcd ohci_platform
     ohci_hcd ehci_platform ehci_hcd sd_mod scsi_mod gpio_button_hotplug usbcore nls_base usb_common
    [  421.475042] CPU: 0 PID: 1421 Comm: rm Tainted: G        W       4.4.14 #8
    [  421.481810] Stack : 803c81a4 00000000 00000001 80420000 83959180 80410e63 803a9890 0000058d
    [  421.481810]    80483790 83b2a800 83573ee0 83573990 00000000 800a678c 803aeed4 80410000
    [  421.481810]    00000003 83b2a800 803ad2e0 82ca7d1c 00000000 800a4758 82d274c8 00000000
    [  421.481810]    8040ff50 801f2b00 00000000 00000000 00000000 00000000 00000000 00000000
    [  421.481810]    00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
    [  421.481810]    ...
    [  421.517209] Call Trace:
    [  421.519663] [<8007197c>] show_stack+0x50/0x84
    [  421.524042] [<80081580>] warn_slowpath_common+0xa0/0xd0
    [  421.529247] [<80081634>] warn_slowpath_null+0x18/0x24
    [  421.534244] [<8011b8a4>] drop_nlink+0x28/0x64
    [  421.538613] [<8015d144>] jffs2_rmdir+0xa0/0xd8
    [  421.543008] [<80111e10>] vfs_rmdir+0x84/0x11c
    [  421.547399] [<80173018>] ovl_cleanup+0x48/0xa0
    [  421.551859] [<80173ef8>] ovl_do_remove+0x3a4/0x428
    [  421.556570] [<80111e10>] vfs_rmdir+0x84/0x11c
    [  421.560900] [<80112930>] do_rmdir+0xfc/0x180
    [  421.565148] [<8006295c>] syscall_common+0x30/0x54
    [  421.569837]
    [  421.571293] ---[ end trace 782b40067d1ca4ff ]---
    [  421.575915] jffs2_rmdir: dir_i->i_nlink is -1

The problem is caused by the incorrect handling of the parent inode's i_nlink count for the dentry to be RENAME_EXCHANGED. There are 3 cases to consider. Assume we want to RENAME_EXCHANGE struct dentry *a and struct dentry *b, and inode_a is pointed to by dentry_a, inode_b is pointed to by dentry_b:  
  1. If inode_a is a directory, but inode_b isn't, then the i_nlink count of old_dir_i must be decreased, whereas the i_nlink of new_dir_i must be increased.  
  2. If inode_a isn't a directory, but inode_b is a directory, then the i_nlink of old_dir_i must be increased, whereas the i_nlink count of new_dir_i must be decreased.  
  3. If the types of inode_a and inode_b are the same, don't change the i_nlink for either old_dir_i or new_dir_i.  
Partly in reference with the implementations of RENAME_EXCHANGE in ubifs, function ubifs_xrename():  
torvalds/linux@9ec6496  
Pull requests have been made to both OpenWrt and LEDE.

