FROM vllm/vllm-openai:gemma4-cu130

RUN set -e && \
    cd /usr/local/lib/python3.12/dist-packages && \
    echo "Applying PR #38909 - fix gemma4 tool parser duplication bug" && \
    curl -fsL https://patch-diff.githubusercontent.com/raw/vllm-project/vllm/pull/38909.diff | \
    git apply --exclude="tests/*" 2>/dev/null || echo "Patch already applied or skipped"
