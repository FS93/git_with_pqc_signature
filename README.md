# Change `git` config to sign commits with PQC Signature

General information on git commit signing:  https://blog.dbrgn.ch/2021/11/16/git-ssh-signatures/

# Instructions
(Here are the commands to do the configuration manually - alternatively adapt the `config` file in this repository and replace the local `.git/config` with it)

0. Setup
	1. Install OQS-OpenSSH fork
	2. `/path/to/OQS-OpenSSH/ssh-keygen -t ssh-dilithium5 -f id_dilithium5`
		 - Generate PQC Keypair (e.g. Dilithium5)
1.  `git config --local commit.gpgsing true`
	- enforce commit signature for every commit (is optional, alternatively use `git commit -S` for a commit) 
2. `git config --local gpg.format ssh`
	- specify key format (alternatives are `x509` and `gpg` (default))
3. `git config --local user.signingKey /path/to/id_dilithium5`
	- specify signing key
4. Create `allowed_signers` file
5. `git config -local gpg.ssh.allowedSignersFile "/path/to/allowed_signers"`
	- specify path to `allowed_signers` 
6. `echo $(git config user.email; cat id_dilithium5.pub) >> allowed_signers`
	- adds public key with the identity ("principal" in SSH terminology) specified in `git config user.email` to `allowed_signers`
7. `git config --local gpg.ssh.program "path/to/OQS-OpenSSH/ssh-keygen"`
	- changes command to sign / verify to `ssh-keygen` from the OQS-OpenSSH fork

-> local settings are located in `.git/config`


# Show / Verify Signatures

`git log --show-signature` (optionally use `--oneline` for shorter presentation)

# Remarks
- for the verification all public keys in `allowed_signers` are tried - if no verification is successful, error `No principal matched` is printed
- it is **NOT** verified if the principal and the commit author are identical, i.e. it is only verified that the commit was done by someone trusted (who could impersonate someone else by changing `git config user.email` and `git config user.name`)
