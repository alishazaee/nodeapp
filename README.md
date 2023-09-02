## Generate Treafik pass
```
echo $(htpasswd -nb user password) | sed -e s/\\$/\\$\\$/g
```
```
ssh-keygen -p -f ~/.ssh/id_rsa -m pem -P "" -N ""
```