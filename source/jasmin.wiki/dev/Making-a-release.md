# Check-list when preparing a release

## Major releases

  - [ ] Fork a `release-YYYY.MM` branch from `main`
  - [ ] Check that CI is working properly on this new branch (is it “protected”? etc.)

## All releases

  - [ ] Check the list of AUTHORS
  - [ ] Proof-read the CHANGELOG
  - [ ] Fix the `version_string`
  - [ ] Prepare announcements for the Formosa website
  - [ ] Put a tag (and push it to the main git repo)
  - [ ] Make a “release” on github, with the sources taken from gitlab.com/jasmin-lang/jasmin-compiler/
  - [ ] Publish into software repositories
      - opam
      - nixpkgs
  - [ ] Update CHANGELOG in main to synchronize with the one from the release