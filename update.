#!/bin/bash
# Auto update semua file ke GitHub & npmjs

echo "======================================"
echo " 🚀 Auto Update Script untuk @kyuuna/baileys"
echo "======================================"

# Tanya mesej commit
read -p "Masukkan mesej commit: " commitMsg

# Tanya jenis version bump
echo "Pilih jenis version bump:"
echo "1) patch (1.0.0 -> 1.0.1)"
echo "2) minor (1.0.0 -> 1.1.0)"
echo "3) major (1.0.0 -> 2.0.0)"
read -p "Pilih (1/2/3): " choice

case $choice in
  1) bump="patch" ;;
  2) bump="minor" ;;
  3) bump="major" ;;
  *) echo "❌ Pilihan tak valid, default ke patch"; bump="patch" ;;
esac

# Git add semua file
git add .

# Commit dengan mesej user
git commit -m "$commitMsg"

# Push ke GitHub
git push origin main

# Bump version ikut pilihan user
npm version $bump

# Publish ke npmjs
npm publish --access public

echo "✅ Selesai! Semua file dah commit + push + npm publish"
