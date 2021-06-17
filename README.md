# MXCuBE Versioning Guide step-by-step

## Initializing the demo repo:
- We are in version 0.1.0 with tag.
- Create branches master, release and develop

## Prepare tag for develop new version 0.2.0:
- Checkout develop
- bump version as
  ```bash
  bumpversion minor -m '[skip ci] new version'
  ```
  This creates the tag `0.2.0.dev0` and the message header deactivates the 
  execution of any [GitHub CI](https://stackoverflow.com/questions/59759921/how-to-skip-github-actions-job-on-push-event/66156840#66156840)
  
- Push develop to origin as:
  ```bash
  git push origin develop --tags
  ```

## Working on the development branch:
- From `develop`, start a feature branch:
  ```bash
  git checkout -b feat-01
  ```
- Commit your changes and push your branch to origin.
  ```bash
  git push origin feat-01
  ```
- Create the PR from `feat-01` to `develop` using the github interface.
- Accepting the PR will merge `feat-01` branch into `develop` (1 commit).
- The `GitHub Action` will use an extra commit to bump and push the new version
  tag into 'develop', in this example would be `0.2.0.dev1`.

- Once the `GitHub Action` is finished, you can pull the repo again and update
  your develop branch and keep working:
  ```bash
  git fetch origin
  git checkout develop
  git pull origin develop
  ```

## Release Candidate procedure:
- Checkout `release` branch and merge `develop` into it.
  ```bash
  git checkout release
  git merge --no-ff develop 
  ```
- Bump the version to release as follows:
  ```bash
  bumpversion release --no-commit
  ```
  This will bump the version creating a new tag as `x.y.z.devN` -> `x.y.z.rc0`

- Modify the commit message by adding the `[skip ci]` on the header, to avoid 
  bumping the version on commit. 

- Push `release` branch to origin.
  ```bash
  git push origin release --tags
  ```
- At this stage, `release` branch is ready for test. See the following section to 
  work on the release branch.

- Checkout `develop` branch and update the version to restart the development
  of a new potential `release` branch.   

  ```bash
  git checkout develop
  bumpversion minor -m '[skip ci] Update minor version'
  ```
  This will bump the version as `x.y.z.devN` -> `x.y+1.0.dev0`. Then you can 
  `develop` to origin. 
  
  ```bash
  git push origin develop --tags
  ```
  After these actions, both `develop` and `release` branches are ready to keep
  working.
  
### Working on the release branch
Any change on the release branch can be committed and pushed directly to origin/release
or can be integrated via `PR` (to be decided). Both actions will automatically bump the 
release candidate version as `x.y.z.rcN -> x.y.z.rcN+1`.

#### Bring the changes to develop branch
The changes committed into the release branch need to be brought
back into `develop` branch MANUALLY, in order to solve the conflicts regarding
the version number. This conflict will be solved by resume the `develop` branch
versioning, as if it was a standard `feature` branch from `develop`.
We cannot use a `PR` in this merge from `release` to `develop`, cause the 
resolution of the conflicts would create a merge from `develop` to `release`,
cause the CI cannot be skipped in the github IDE interface. So:

```bash
git checkout develop
git merge --no-ff release
```
At this point you need to solve the conflicts regarding the versioning by
resuming the `develop` version. Once fixed, commit your changes to `develop`
which will trigger the version bumping automatically.

```bash
git push origin develop
```

Finally, update your local `develop` branch to keep developing on:

```bash
git pull origin develop
```



## Final Release procedure

- Once accepted the release candidate branch, Checkout the `master` branch and 
  merge the `release`branch.
  ```bash
  git checkout master
  git merge --no-ff release
  ```
  
- Bump the version to release as follows:
  ```bash
  bumpversion release --no-commit
  ```
  This will bump the version creating a new tag as `x.y.z.rcN` -> `x.y.z`

- Modify the commit message by adding the `[skip ci]` on the header, to avoid 
  bumping the version on commit. 

- Push `master` branch to origin.
  ```bash
  git push origin master --tags
  ```
- At this stage, `master` branch is ready for test.


  (Any change on the master branch MUST be committed directly into it and merged
  back into develop via `PR`. This `PR` will have conflicts regarding the version
  number, which MUST follow the develop branch versioning, as if it would be 
  a `feature` branch.)


### Working on the master branch (hot fix)
Any change on the release branch can be committed and pushed directly to origin/master
or can be integrated via `PR` (to be decided). Both actions will automatically bump the 
master version as `x.y.z -> x.y.z+1`.

#### Bring the changes to develop branch
The changes committed into the master branch need to be brought
back into `develop` branch MANUALLY, in order to solve the conflicts regarding
the version number. This conflict will be solved by resume the `develop` branch
versioning, as if it was a standard `feature` branch from `develop`.
We cannot use a `PR` in this merge from `master` to `develop`, cause the 
resolution of the conflicts would create a merge from `develop` to `master`,
cause the CI cannot be skipped in the github IDE interface. So:

```bash
git checkout develop
git merge --no-ff master
```
At this point you need to solve the conflicts regarding the versioning by
resuming the `develop` version. Once fixed, commit your changes to `develop`
which will trigger the version bumping automatically.

```bash
git push origin develop
```

Finally, update your local `develop` branch to keep developing on:

```bash
git pull origin develop
```
