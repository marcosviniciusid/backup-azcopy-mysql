# Backup Mysql Automatizado

Backup de banco de dados mysql em servidor linux para um Storage Account no Azure.
Backup via mysqldump e enviado para STO com azcopy.

```
#!/bin/bash

# ===========================================
# Backup externo do banco de dados mysql
# version 1, atualizado em 08 mar, 2022
# copyright 2022 Marcos Vinicius @empresa
# ============================================

# Data de Hoje
data=$(date +\%-d-\%-m-\%-Y)

# Variaveis para mysqldump 
dbname='nome'
dbuser='usuario'
dbpass='senha'

# Arquivo
path='/home/backup/diario'
file=backup-$dbname-$data.sql
filecompacted=$file.tar.gz

# Token SAS
sas='https://nomeblob.blob.core.windows.net/container/$dbname/db/?sp=chave'

# Backup do banco de dados
mysqldump --user=$dbuser --password=$dbpass $dbname > $path/$file

# Compactar arquivo
tar -cvzf $path/$filecompacted $file

# AzCopy -> Storage Account (stowebapp)
/home/az_copy/azcopy cp $path/$filecompacted "$sas"

# Deletar arquivo local
rm -rf $path/$filecompacted
rm -rf $path/$file
```
