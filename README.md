name: Size Label

on:
  pull_request:
    types:
      - opened
      - synchronize

jobs:
  analyze:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
        
      - name: Install Node.js
        uses: actions/setup-node@v2
        with:
          node-version: 14

      - name: Install dependencies
        run: npm ci

      - name: Run Antora
        run: npx antora antora-playbook.yml
        
      - name: Analyze changes
        id: changes
        run: |
          SIZE=$(git diff --no-color --numstat origin/main...HEAD -- _site | awk '{ sum += $1 } END { printf "%.0f", sum }')
          echo "::set-output name=size_changes::$SIZE"
        
  label:
    needs: analyze
    runs-on: ubuntu-latest

    steps:
      - name: Set PR label
        uses: actions/github-script@v4
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          script: |
            const sizeChanges = parseInt(process.env.SIZE_CHANGES);
            const context = github.context.payload.pull_request;
            const owner = context.base.repo.owner.login;
            const repo = context.base.repo.name;
            const pull_number = context.number;
            let labelName = '';
            
            if (sizeChanges < 10) {
              labelName = 'Size: XS';
            } else if (sizeChanges < 50) {
              labelName = 'Size: S';
            } else if (sizeChanges < 100) {
              labelName = 'Size: M';
            } else if (sizeChanges < 500) {
              labelName = 'Size: XL';
            } else {
              labelName = 'Size: XXL';
            }
            
            github.issues.addLabels({
              owner: owner,
              repo: repo,
              issue_number: pull_number,
              labels: [labelName]
            });
