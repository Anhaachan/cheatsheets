# db volume usage
### Step 1:
```sh
docker volume create <a-branch-volume>
```

```bash
docker run --rm --volumes-from <container-name> -v <a-branch-volume>:/backup busybox sh -c "cp -r /var/lib/postgresql/data/* /backup/"
```

### Step 2:
`git switch <b-branch>`

make some brilliant changes, commit it. push it

### Step 3:
`git switch <a-branch>`

#### Approach 1:
`docker-compose down`

```sh
docker run --rm --volumes-from <db-container> -v <a-branch-volume>:/backup busybox sh -c "cp -r /backup/* /var/lib/postgresql/data/"
```

`docker-compose up -d` or `docker-compose down up -d --build` for fresh start

#### Approach 2(without compose override):
```yaml
    ...
    ... # main code
    volumes:
      - <a-branch-volume>:/var/lib/postgresql/data

volumes:
  <a-branch-volume>:
    external: true
```

`docker compose up -d --build`



# db count column from table

```sql
SELECT COUNT(*) FROM information_schema.columns WHERE table_schema = 'shema_name_usually_public' AND table_name = 'table_name';
```
