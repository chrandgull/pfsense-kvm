# pfsense-kvm
Recently I was setting up a virtualized pfSense v 2.4.4 CE instance under KVM. The host computer had a Gigabyte 990FXA-UD3 motherboard, and a AMD FX-8120 processor. After doing a bit of reading and configuration tweaking, I was able to get it started with Host (CPU) passthrough + AES-NI support + a physical four port gigabit ethernet card passed through.

I ran into a few problems I thought I'd document in case someone else had the same problems.

Problem number 1: 

During the boot up of pfsense, it would freeze after a few seconds as described at https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=213333. This only occured when CPU passthrough was active. Emulating an x86 CPU worked fine, but was slow. It also prevented the use of AES-NI CPU instructions, which is pretty helpful to have when running pfSense.

The key to get it to work was using the following CPU definition:

  <cpu mode='custom' match='exact' check='partial'>
    <model fallback='allow'>Westmere</model>
  </cpu>
  
  I think this is just restricting the features of the virtual CPU while still executing the CPU instructions directly (without emulating them) but I'm not sure. It does allow the VM to boot. 
  
  
  Problem number 2: 
  
  There is a physical intel EXPI9404VT gigabit ethernet card in the host machine. PCI passthrough worked fine for other VMs, but every time Pfsense booted I could only see one port on the card. Changing the machine type to Q35-3.0 from the default(Q35-4.1) allowed all the ports to be seen.
  
  





