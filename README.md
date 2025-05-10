```
#!/usr/bin/env bash
# Usage: ./clean-node-modules.sh /path/to/target-folder

TARGET_DIR="${1:-.}"

echo "正在搜索所有 node_modules 文件夹: $TARGET_DIR"
NODE_MODULES_DIRS=$(find "$TARGET_DIR" -type d -name "node_modules" -prune)
TOTAL=$(echo "$NODE_MODULES_DIRS" | grep -c ".")
if [ "$TOTAL" -eq 0 ]; then
  echo "未找到任何 node_modules 文件夹。"
  exit 0
fi

echo "$NODE_MODULES_DIRS" > /tmp/node_modules_dirs.txt
COUNT=0
echo "共找到 $TOTAL 个 node_modules 文件夹。"

# 并行删除并显示进度
cat /tmp/node_modules_dirs.txt | awk '{print NR"\t"$0}' | xargs -P 8 -n 1 -I {} bash -c '
  IDX=$(echo {} | cut -f1)
  DIR=$(echo {} | cut -f2)
  echo "[$IDX/$TOTAL] 正在删除: $DIR"
  rm -rf "$DIR"
'
echo "所有 node_modules 文件夹已并行删除。"

```
