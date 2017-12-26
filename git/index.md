###### удаление внешнего репозитория
```bash
git remote rm origin
```
###### подключение внешнего репозитория

```bash
git remote add origin https://github.com/******/notes.git

```

###### получение изменений

* git diff-tree -r --no-commit-id --name-only --diff-filter=ACMRT HEAD^ b09ede | xargs tar -rf release.tar
  выбрать все измененые файлы от предыдущего состояния до последнего мердж коммита

* git diff-tree -r --no-commit-id --name-only --diff-filter=ACMRT HEAD~4 133ffffa | xargs tar -rf branch.tar
  выбрать все измененые файлы начиная от 4 коммитов до последнего состояния.

