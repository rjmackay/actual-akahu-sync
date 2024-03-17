Sync [ActualBudget](https://actualbudget.com/) via [Akahu](https://akahu.nz/)

## PREREQUISITES
  - A [Akahu user access token](https://my.akahu.nz/)
  - The [ActualBduget Budget ID](https://actualbudget.com/docs/developers/using-the-API/#getting-started)
  - ActualBudget needs to be installed and running
  - Once you get everything running, you may (optionally) configure a system cron to run this automatically.

## IMPORTANT NOTES
 - SimpleFin data updates one time / day, for each linked account. The time that it updates may vary, even from day to day (based on the bank and upstream provider, MX).
 - The first run syncs back to the start of the current month. I would reconcile all accounts up to the start of the current month, either manually or via CSV import, and then run this.
 - You will want to check (for the first run) for any transactions right around the 1st of the current month, to make sure nothing is left out.
 - Future runs start from 5 days prior to the previous run, to catch any that processed later. No duplicate transactions will be created in Actual.
 - The max length of history returned varies from bank to bank, so I would make sure to run this monthly at a minimum. Some have a number of transactions limit, others return between 2-6 months of history.
 - You can reset all your settings by deleting the config.json file, and the budget folder, if you wish. This will not delete previous transactions from Actual.

## TODO
 - Better security for storage of SimpleFIN AccessKey
 - Allow custom date ranges for sync
 
## USAGE
  - **Sync** - If the app hasn't been configured yet, you'll be run through the setup steps, otherwise it will sync the current month for all accounts. 
    ```
    node app.js
    ```

  - **Setup** - Change your SimpleFIN token, which budget file to use, your Actual Budget url, Actual Budget Password,and how the accounts are linked. 
    ```
    node app.js --setup
    ```

  - **Link** - Change or add any new linked accounts 
    ```
    node app.js --link
    ```
    
## TROUBLESHOOTING

- I've had some users report trouble using .local addresses to connect. If you run into this and are able, a reverse proxy might be of assistance.

- I've had a couple users report segfaults using Node v21.x. I haven't been able to recreate, but for those that have, switching to Node 20.x, deleting and re-cloning, and setting up again (with a new SimpleFin setup token) has fixed the issue both times.

- Be sure you have a budget, with encryption password (or leave blank with none), that has at least 1 account. You MUST have at least 1 account created, so you have something to map SimpleFin accounts to.

## RUNNING IN DOCKER

I like to use docker compose in the following way:

```bash
sudo docker compose build
```

```bash
sudo PROFILE_NAME=my-bank docker compose run --rm app
```
Just like non-docker, the first time it is run with a given PROFILE_NAME it will go through the setup process. On subsequent runs with the same PROFILE_NAME, it will run a sync and exit. This makes it ideal for scheduling with a cron job.

The configurations are stored in docker volumes for a layer of obfusctation. These could feasibly be encryted in the container in future versions.

A pre-built docker image is available [here](https://hub.docker.com/r/oddomatik/actual-simplefin-sync).
