# GIT ET SES COMMANDES

*Quelques commandes git avec ou sans explications*


- **Export (sans le .git)**

```
git clone --depth=1 git@github.com:xxx/yyy.git && rm -rf yyy/.git
```

- **Archive**
 
On peut exclure des fichiers dans l'archive grâce au fichier .gitattributes : <a href="http://feeding.cloud.geek.nz/posts/excluding-files-from-git-archive">http://feeding.cloud.geek.nz/posts/excluding-files-from-git-archive</a>

```
git archive --format zip --output /full/path/to/zipfile.zip master
```