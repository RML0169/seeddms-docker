# Information

Builds a docker image for seeddms (https://www.seeddms.org).

This image supports OCR processing for images and PDFs out of the box. Other types can be configured and converted using web interface.
Cron is also included to handle jobs internally (backup, index, ...).

## How to run

`docker run --name seeddms -d -v dms-data:/var/www/seeddms/data -p 8080:80 nlippke/seeddms:5.1.13`

or as compose file

```yaml
version: '2'

services:
  dms:
    image: nlippke/seeddms:5.1.20
    ports:
      - "8080:80"
    environment:
      - TZ=Europe/Berlin
      - 'CRON_INDEX=0 0 * * *'
      - 'CRON_BACKUP=0 23 * * *'
    mem_limit: 2g
    volumes:
      - dms-data:/var/www/seeddms/data
      - /share/Container/container-data/seeddms/extensions:/var/www/seeddms/seeddms/ext
      - /share/Container/container-data/seeddms/backup:/var/www/seeddms/backup
      - /share/Container/container-data/seeddms/import:/var/www/seeddms/import
    logging:
      options:
        max-size: "10m"
        max-file: "1"
volumes:
  dms-data:
```

## Default configuration

The image is preconfigured. Nevertheless you're guided through the installation steps upon first start for a review.

1. `/var/www/seeddms/data` is the central data directory. It is not intended to be bound to a host directory. Instead use a docker volume.
2. `/var/www/seeddms/backup` is where backups are being stored. Bind it to a host directory.
3. `var/www/seeddms/import` is being used as drop folder. Bind it to a host directory.
4. Optionally mount `/var/www/seeddms/seeddms/ext` to allow upload of extensions.

Backup and import directories should be readable/writeable by uid 33!

## Backup

Backup is done by syncing the `data` folder (partially) to the backup folder. Use environment variable `CRON_BACKUP` for automatic scheduling.

## Full text search

Indexing documents can take some time (especially on low powered NAS). Therefore indexing is done asynchronously by a job. Use `CRON_INDEX` for scheduling this job. 

## Additional information

The image is base on https://github.com/ludwigprager/docker-seeddms.

