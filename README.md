name: Size label
on: pull_request_target
jobs:
  size-label:
    permissions:
      contents: read
      pull-requests: write
    runs-on: ubuntu-latest
    steps:
      - name: Size label
        uses: actions/checkout@v2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
      - name: Calculate size
        run: |
          git fetch --update-shallow
          git diff --stat HEAD~1
          size=$(cat .git/FETCH_HEAD | wc -l)
      - name: Add label
        run: |
          if [ $size -gt 100 ]; then
            echo "Adding size: L"
            hub label add ${{ github.head_repo.full_name }} ${{ github.head_ref }} "size: L"
          elif [ $size -gt 30 ]; then
            echo "Adding size: M"
            hub label add ${{ github.head_repo.full_name }} ${{ github.head_ref }} "size: M"
          else
            echo "Adding size: S"
            hub label add ${{ github.head_repo.full_name }} ${{ github.head_ref }} "size: S"
          fi
