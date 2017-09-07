FiaaS operations
================

This repo uses https://git-secret.io to manage secrets, you must have it installed and be added
before you can use anything in this repository.

Reading secrets
---------------

To decrypt everything in the repo, use `git secret reveal`


Adding a new secret
-------------------

If the secret is linked to a new site, create a new file named `<site>.txt`, and add it to the
repo: `git secret add <filename>`

Edit the file for the site related to the secret in question, adding a description and a value.

When done, re-encrypt all files with `git secret hide`, and commit your changes. You should 
make sure to push your changes as soon as possible, as merging encrypted files is difficult.


Adding a new user
-----------------

For a new user to get access, and existing user must grant them access. The following steps
grants access for a new user:

- Import the new users GPG public-key into your local keyring: `gpg --import <key>`
    - If you follow the person on https://keybase.io, you can get the key 
    and import it using keybase: `keybase pgp pull` 
- Add their key to the known users in the repo: `git secret tell <email>`
- Re-encrypt all files: `git secret hide`
- Push your changes


Removing a user
---------------

- `git secret killperson <email>`
- Re-encrypt all files: `git secret hide`

Keep in mind that after this, the person in question will no longer be able to decrypt the
current set of files, and new changes going in. But since this is a git repo, everything
in the history of the repo that was encrypted while the user had access **is still readable**.

If you truly want to revoke their access, you must first do the above steps, then rotate 
every secret in the repository!


Some security tips
------------------

### Add a pre-commit hook

Forgetting to do `git secret hide` could result in commiting unencrypted files. To mitigate
this, consider adding a pre-commit hook to git:

`echo git secret hide >> .git/hooks/pre-commit`


### Adding a user only requires git commit rights

It's possible to add a user to the repo using the `git secret tell` command **without**
already having access to the encrypted files. They would still not have access to decrypt
anything, but if someone with access re-encrypts everything (because they are adding a
different user), all added users gets access.

Make sure to not accept changes to `.gitsecret/keys/` without knowing what has changed.
