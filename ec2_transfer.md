# EC2 File Transfer Steps

1. Create two EC2 instances on AWS (Ubuntu os):

- `vm1` — sender
- `vm2` — receiver
  Use the same key pair (`.pem`) and the same security group for both instances.

2. Configure the security group (Inbound rules):

- SSH: TCP 22
- ICMP (All) — to allow `ping` between instances
- HTTP: TCP 80 (if needed)
- HTTPS: TCP 443 (if needed)

3. From your local machine, copy the `.pem` file to `vm1` so `vm1` can authenticate to `vm2` (if both instances use the same key):

```
scp -i /path/to/your/keypair.pem /path/to/your/keypair.pem ubuntu@<vm1-public-ip>:/home/ubuntu/
```

4. SSH into `vm1` from your local machine:

```
ssh -i /path/to/your/keypair.pem ubuntu@<vm1-public-ip>
```

5. On `vm1`, verify the `.pem` file exists and set correct permissions:

```
ls -l /home/ubuntu/
chmod 400 keypair.pem
```

6. Create a test file on `vm1` (or transfer one from your local machine). Example using echo:

```
 nano test.txt
```
add content to it


7. From `vm1`, transfer `test.txt` to `vm2` using the `scp` command and the private IP of `vm2`:

```
scp -i keypair.pem test.txt ubuntu@<vm2-private-ip>:/home/ubuntu/
```

Notes:

- If `scp` over the private IP fails, ensure the security group allows SSH from `vm1` to `vm2` (either by allowing the same security group or the private IP range).
- If you prefer, you can `scp` directly from your local machine to `vm2` using `vm2`'s public IP instead of routing via `vm1`.

8. SSH into `vm2` and verify the file transfer:

```
ssh -i /path/to/your/keypair.pem ubuntu@<vm2-public-ip>
ls -l /home/ubuntu/
cat /home/ubuntu/test.txt
```

9. Cleanup (optional): remove the copied key from `vm1` when finished:

```
rm /home/ubuntu/keypair.pem
```

--
These steps assume Ubuntu AMIs and `ubuntu` as the default username; adjust the username accordingly for other AMIs.
