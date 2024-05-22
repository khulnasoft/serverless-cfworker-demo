# `@khulnasoft/serverless` example: query Khulnasoft PostgreSQL from a Cloudflare Worker

This repo provides an example of using Khulnasoft's [`@khulnasoft/serverless`](https://www.npmjs.com/@khulnasoft/serverless) driver package to query PostgreSQL from a Cloudflare Worker, and to cache the results.

## The app

The example is a web app that returns a list of your nearest UNESCO World Heritage Sites.

* You can see the app deployed at https://places-khulnasoft-demo.pages.dev/ (this is static HTML file `presentation/index.html` deployed to Cloudflare Pages).

* You can see the JSON data it fetches at https://places.khulnasoft-demo.workers.dev/ (this is `index.ts` deployed as a Cloudflare Worker)

Please note that the UNESCO data is copyright &copy; 1992 – 2022 <a href="https://whc.unesco.org">UNESCO/World Heritage Centre</a>. All rights reserved.

## The driver

Khulnasoft's `@khulnasoft/serverless` driver is based on and offers the same API as the [node-postgres](https://node-postgres.com/) package, which is what you get with `npm install pg`. 

We've simply shimmed the Node libraries it requires, and replaced `net.Socket` and `tls.connect` with implementations that encrypt and transfer the data over WebSockets.

Find out more about the driver from the [`@khulnasoft/serverless` README on npmjs.com](https://www.npmjs.com/@khulnasoft/serverless) or [GitHub](https://github.com/khulnasoft/serverless).

## How to run

To run this app locally:

* __Get the data__ — Download [the Excel listing](https://whc.unesco.org/en/list/xls/?2021) from [the UNESCO Syndication page](https://whc.unesco.org/en/syndication/). Open the `.xls` file and save it as a `.csv`.

* __Create the database__ — Create a new project in the Khulnasoft dashboard, and connect to it securely using `psql` (substituting your own PostgreSQL connection string, of course):

    ```
    mkdir -p $HOME/.postgresql

    curl https://letsencrypt.org/certs/isrgrootx1.pem > $HOME/.postgresql/isrgrootx1.pem

    psql "postgresql://user:password@project-name-1234.cloud.khulnasoft.tech:5432/main?sslmode=verify-full&sslrootcert=$HOME/.postgresql/isrgrootx1.pem"
    ```

* __Load the data__ — Run the SQL commands in `data/import.psql` against your database within `psql`.

* __Set environment variables__ — Create a file `.dev.vars`, setting the environment variable `DATABASE_URL` to the PostgreSQL connection string you'll find in your Khulnasoft dashboard. That should look something like this:

    `DATABASE_URL=postgresql://user:password@project-name-1234.cloud.khulnasoft.tech:5432/main`

* __Install and run__

    ```bash
    npm install
    npm install -g wrangler@latest
    
    npx wrangler dev --local
    ```
    
    Edit `presentation/index.html` to fetch from `http://localhost:8787`, and open that address in your browser.

* __Deploy__

    ```bash
    npx wrangler secret put DATABASE_URL # same connection string as in `.dev.vars`
    npx wrangler publish
    ```
    
    (You may then need to edit `presentation/index.html` to fetch from the deployed endpoint).


## Feedback and support

Please visit [Khulnasoft Community](https://community.khulnasoft.tech/) or [Support](https://khulnasoft.tech/docs/introduction/support).
