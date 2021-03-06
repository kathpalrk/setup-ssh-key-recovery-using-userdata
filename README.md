# Recover Lost SSH Key in EC2 using UserData

Sometimes ssh keys gets mis-placed or lost completely that you need to recover access to your server without them. In this article we are going to see how you can gain access to your server with a new set of keys using userdata field with minimal downtime to your environment.


![Execute user data every boot](https://github.com/kathpalrk/setup-ssh-key-recovery-using-userdata/blob/master/images/setup-ssh-key-recovery-using-userdata-rk-00.png)

### Prerequisites
1. `Server WO Key` - EC2 Instance  - _Without Keys_
1. `Recovery Server` - EC2 Instance

### Generate the Authorized Keys from `Recovery Server`
Login to the `Recovery Server` and execute the following command and copy the output as shown. _Leave out the red underlined text_
```sh
more /home/ec2-user/.ssh/authorized_keys
```
![Execute user data every boot](https://github.com/kathpalrk/setup-ssh-key-recovery-using-userdata/blob/master/images/setup-ssh-key-recovery-using-userdata-rk-01.png)

### Update the key in the `Server WO Key`
1. Stop the server.
1. Update the user data field with the code updated with the key.
   - In the below commands update your recovery key, after `echo -e`  _between quotes_
   ```sh
   Content-Type: multipart/mixed; boundary="//"
   MIME-Version: 1.0
   
   --//
   Content-Type: text/cloud-config; charset="us-ascii"
   MIME-Version: 1.0
   Content-Transfer-Encoding: 7bit
   Content-Disposition: attachment; filename="cloud-config.txt"
   
   #cloud-config
   cloud_final_modules:
   - [scripts-user, always]
   
   --//
   Content-Type: text/x-shellscript; charset="us-ascii"
   MIME-Version: 1.0
   Content-Transfer-Encoding: 7bit
   Content-Disposition: attachment; filename="userdata.txt"
   #!/bin/bash
   /bin/echo -e "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCd/RXkjSGf1unDAPefX06ijo2y1VpkBrCc/skuRAvyjeKiSypXMR70De2Yl5qYkXzhkJckviui1TNoDgPN5JpSGVh04i8LG9OjhKIOjrj8+5qqONjOteIEiveBfY2OB/XRGm5yKLQiW/fPsJusVTMdPO7NQL+n3zc0ikMNCR1j16wNsciWLUiRzD269n/4ant3S2fTNXMK+1X+fb+UObhTd0VtjcIqDbYq/N6pAdt2frn0V99m47uQ7C+iqOMDtCE7eS+wjvrCSyOqEBYjwnJe1QDaPNQjeDnP/3sbk3tvAw5kszCsOBRu8OaPOjShQeVqwpfoBSfEKPdRFpVGdWwh rahul@rahulk.example.com
" >> /home/ec2-user/.ssh/authorized_keys
   --//
   ```
1. Start the server
1. Login to `Server WO Key` with the `Recovery Server` Key

##### References
[1] - [AWS Docs](https://aws.amazon.com/premiumsupport/knowledge-center/execute-user-data-ec2/)

