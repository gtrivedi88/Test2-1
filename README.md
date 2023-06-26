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
      - name: Install hub
        run: |
          sudo apt-get update
          sudo apt-get install -y hub
      - name: Calculate size
        run: |
          if git rev-parse HEAD~1 >/dev/null 2>&1; then
            git fetch origin main --update-shallow
            git diff --stat HEAD~1
            size=$(cat .git/FETCH_HEAD | wc -l)
          else
            echo "No previous commits"
            size=0
          fi
      - name: Add label
        run: |
          if [ $size -gt 100 ]; then
            echo "Adding size: L"
            curl -X POST -H "Authorization: Bearer ${{ secrets.GITHUB_TOKEN }}" \
              -H "Accept: application/vnd.github.v3+json" \
              https://api.github.com/repos/${{ github.repository }}/issues/${{ github.event.pull_request.number }}/labels \
              --data '["size: L"]'
          elif [ $size -gt 30 ]; then
            echo "Adding size: M"
            curl -X POST -H "Authorization: Bearer ${{ secrets.GITHUB_TOKEN }}" \
              -H "Accept: application/vnd.github.v3+json" \
              https://api.github.com/repos/${{ github.repository }}/issues/${{ github.event.pull_request.number }}/labels \
              --data '["size: M"]'
          else
            echo "Adding size: S"
            curl -X POST -H "Authorization: Bearer ${{ secrets.GITHUB_TOKEN }}" \
              -H "Accept: application/vnd.github.v3+json" \
              https://api.github.com/repos/${{ github.repository }}/issues/${{ github.event.pull_request.number }}/labels \
              --data '["size: S"]'
          fi
