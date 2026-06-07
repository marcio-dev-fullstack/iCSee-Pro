Copie e execute esta linha única no seu terminal:

```bash
git add -A && git commit -m "Commit blindado e completo" && git branch -M main && git remote remove origin && git remote add origin https://github.com/marcio-dev-fullstack/iCSee-Pro.git && git pull origin main --rebase && git push -u origin main --force