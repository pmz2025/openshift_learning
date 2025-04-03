# Openshift Info

## Enable autocompletion on zsh

cat >>~/.zshrc<<EOF
if [ $commands[oc] ]; then
  source <(oc completion zsh)
  compdef _oc oc
fi
EOF


The server is accessible via web console at:
  https://console-openshift-console.apps-crc.testing

Log in as administrator:
  Username: kubeadmin
  Password: Ap68H-pLuQp-S2WC4-NRdYu

Log in as user:
  Username: developer
  Password: developer

Use the 'oc' command line interface:
  $ eval $(crc oc-env)
  $ oc login -u developer https://api.crc.testing:6443


## Reference:

https://docs.redhat.com/de/documentation/red_hat_build_of_microshift/4.12/html/cli_tools/cli-configuring-cli#cli-enabling-tab-completion-zsh_cli-configuring-cli

To login as a regular user, run 'oc login -u developer -p developer https://api.crc.testing:6443'.
To login as an admin, run 'oc login -u kubeadmin -p X7Yx4-xgHi6-PfxVT-Uq4Xq https://api.crc.testing:6443'
