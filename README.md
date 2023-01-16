# Backup Mysql Automatizado

Backup de banco de dados mysql em servidor linux para um Storage Account no Azure.
Backup via mysqldump e enviado para STO com azcopy.

```bash
#!/bin/bash

# ===========================================
# Backup externo do banco de dados mysql
# version 1, atualizado em 16 jan, 2023
# copyright 2023 Marcos Vinicius @empresa
# ============================================

# Data de Hoje
data=$(date +\%-d-\%-m-\%-Y)

# Variaveis Para Mysqldump 
dbname='nome'
dbuser='usuario'
dbpass='senha'

# Arquivo
path='/home/backup/diario'
file=backup-$dbname-$data.sql
filecompacted=$file.tar.gz

# Variaveis do Blob/Container
blob='nome-storage-account'
container='nome-container-blob'
token='sas-do-container-blob'

# Token SAS
sas="https://$blob.blob.core.windows.net/$container/$dbname/db/?$token"

# Backup do Banco de Dados
mysqldump --user=$dbuser --password=$dbpass $dbname > $path/$file

# Compactar Arquivo
tar -cvzf $path/$filecompacted $path/$file

# AzCopy -> Storage Account (stowebapp)
/home/az_copy/azcopy cp $path/$filecompacted "$sas"

# Deletar Arquivo Local
rm -rf $path/$filecompacted
rm -rf $path/$file
```
