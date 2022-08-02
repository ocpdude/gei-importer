## Using GitHub Enterprise Importer
Demo video: https://youtu.be/e_CfZcse1b0 \
GitHub Documenation: https://docs.github.com/en/enterprise-cloud@latest/early-access/github/migrating-with-github-enterprise-importer

Linux:
1. git (included in distros)
2. gh (GitHub CLI) : https://github.com/cli/cli/blob/trunk/docs/install_linux.md
3. gei extension : `gh extension install https://github.com/github/gh-gei`
4. Powershell : https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-linux?view=powershell-7.2

MacOS:
1. git : `brew install git`
2. gh (GitHub CLI) : `brew install gh`
3. gei extension : `gh extension install https://github.com/github/gh-gei`
4. Powershell : https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-macos?view=powershell-7.2

Windows:
1. git : https://gitforwindows.org/
2. gh (GitHub CLI) : `winget install --id GitHub.cli`
3. gei extension : `gh extension install github/gh-gei`
4. Powershell (included in distros)

GitHub CLI : https://github.com/cli/cli#installation

For GitHub Enterprise Server (GHES) migrations to GitHub Enterprise Cloud (GHEC), you will need a Azure Storage Account. Azure provides learning credits on their platform ~$200USD that mayb be used for this purpose. We'll use access to the Azure Storage Blob (S3) as a transfer layer during the migration. This step is NOT needed if going from one GHEC to another GHEC.

### Migration
https://docs.github.com/en/enterprise-cloud@latest/early-access/github/migrating-with-github-enterprise-importer/running-a-migration-with-github-enterprise-importer/running-a-migration-to-github-enterprise-cloud

1. Create a Personal Access Token on the source system and export it as `GH_SOURCE_PAT`
2. Create a Personal Access Token on the destination system and export it as `GH_PAT`
3. On the target system, create your DESTINATION_ORG (Authorize if using EMU)
3. Generate the migration script \
`gh gei generate-script --github-source-org ${SOURCE_ORG} --github-target-org ${DESTINATION_ORG} --download-migration-logs --output ${FILENAME.ps1}` \
Optional for GHES : `--no-ssl-verify --ghes-api-url ${GHES_API_URL} --azure-storage-connection-string "${AZURE_STORAGE_CONNECTION_STRING}`
4. Optional: `chmod +x ${FILENAME.ps1}`
5. Execute migration script. \
    i. Linux : ./${FILENAME.ps1} \
    ii. Windows : .\${FILENAME.ps1}

### Dealing with Mannequins
https://docs.github.com/en/enterprise-cloud@latest/early-access/github/migrating-with-github-enterprise-importer/running-a-migration-with-github-enterprise-importer/reclaiming-mannequins

Bulk Migration
1. Generate mannequin csv file : `gh gei generate-mannequin-csv --github-target-org ${DESTINATION_ORG} --output mannequin.csv`
2. Edit the csv file and insert the reattribution user name at end of each line, remove 'dependabot' if you have this entry.
3. Execute the reattribution script : `gh gei reclaim-mannequin --github-target-org ${DESTINATION_ORG} --csv mannequin.csv`