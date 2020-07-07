nfs：https://www.cnblogs.com/scaven-01/p/11461908.html   

注：如果显示mount.nfs: access denied by server while mounting 

可以将服务端的/etc/exports里的ip改为*试试：如/data/  *(rw,sync,root_squash)，或是获取准确的ip网段
