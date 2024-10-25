# Set Up for Vite Builds

## [Preview Workflow](https://github.com/marketplace/actions/deploy-pr-preview)

### Requirements
- A workflow file that deploys to GitHub Pages ex. `main.yaml`
- A workflow file for PR previews ex. `preview.yaml`
- `base` should be set to `""` in `vite.config.js`
- GitHub Pages should be deployed to the `gh-pages` branch
    - Where can you set this?

    ```
    ├── (Repository) Settings
    │   ├── Code and Automation
    │   │   ├── Pages
    └── ...
    ```
- Read and write permissions should be set
    - Where can you set this?

    ```
    ├── (Repository) Settings
    │   ├── Code and Automation
    │   │   ├── Actions
    │   │   │   ├── General
    │   │   │   │   ├── Workflow Permissions
    └── ...
    ```

### Output
- The URL generated would be something like `https://<USERNAME>.github.io/<REPONAME>/<UMBRELLA_DIR>/pr-<PR_NUMBER>`
    - `UMBRELLA_DIR` is configured in `preview.yaml`

### Notes
- In order to provide the path at which GitHub Pages deploys to, `base` is added to `vite.config.js`
    - For example, if you want to deploy to `https://<USERNAME>.github.io/blah`, `base` is `/blah/`
    - The PR preview workflow actually creates a folder within the `gh-pages` branch and another folder **within `umbrella-dir`** for the respective PR

        - The preview deploys from the sub-folder within `umbrella-dir`

        - Therefore, it is **very important** that the path in the below tags in `dist/index.html` actually point to the files needed to load the website!!! Namely, within the sub-folder or from `/<UMBRELLA_DIR>/pr-<PR_NUMBER>`
        ```
        <link rel="icon" type="image/svg+xml" href="./vite.svg" />
        ...
        <script type="module" crossorigin src="./assets/index-YWNzDFRL.js"></script>
        <link rel="stylesheet" crossorigin href="./assets/index-n_ryQ3BS.css">
        ```
        - In this case, `base` has to be set to `""` for the `./` to appear, which points to the sub-folder where GitHub Pages is deployed from

        - If `base` is `/blah`, the tags would be the below:
        ```
        <link rel="icon" type="image/svg+xml" href=/blah/vite.svg" />
        ...
        <script type="module" crossorigin src="/blah/assets/index-YWNzDFRL.js"></script>
        <link rel="stylesheet" crossorigin href="/blah/assets/index-n_ryQ3BS.css">
        ```
        This is not correct because GitHub Pages will try to request files from `https://<USERNAME>.github.io/<REPONAME>/<UMBRELLA_DIR>/pr-<PR_NUMBER>/blah`, which does not exist


### Debugging
- Getting a `Resource not accessible by integration`
    - Make sure read and write permissions are set for workflow. Refer to [Requirements](#requirements)
 
- Getting a `404 not found`
    - Since the deploy workflow `main.yaml` deploys using the build `folder: dist`, you can test that your production build looks and works ok with `npm run build && npm run preview`
        - `npm run preview` should serve the files in `dist` after building

    - Look at [Notes](#notes) for the annoying error
