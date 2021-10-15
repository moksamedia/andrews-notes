## Links
https://github.com/Fmstrat/winapps
https://reposhub.com/linux/miscellaneous/Fmstrat-winapps.html
https://github.com/Fmstrat/winapps/blob/main/docs/KVM.md

## Notes

Have to install KVM. Then install winapps. 

KVM installs a service named `libvirtd`.

  `virt-manager` is the graphical virtual machine manager
  `virsh` is the shell manager
  
  `bin/winapps check` from ~/winapps/ directory checks to make sure `RDPWindows` is started
  
  `virsh start RDPWindows` starts the virtual machine
  
