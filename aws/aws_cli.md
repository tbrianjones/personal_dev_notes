AWS CLI
=======

Tips & Tricks
-------------
- configure multiple accounts: http://www.tothenew.com/blog/using-multiple-iam-accounts-through-aws-cli-tool/
- enable auto-complete:
  - add to `/etc/bashrc`
  - `complete -C aws_completer aws`
- change default output of CLI commands
  - defaults to JSON
  - alter the `~/.aws/config`
  - set `output` to table (best for humanz), json, or text
