# Backlog

Ideas and cross-cutting concerns not yet ready for a spec.

- **Group account-related specs.** Sign-in (001–004), sign-out (005),
  profile (006) and request access (007) are all "account" features.
  Decide how to group them (e.g. an `account/` folder, a parent spec, or a
  numbering range) and whether they share a navigation area in the app.

- **Email.** Not yet specified: what the app sends (e.g. access request
  updates, inactivity warnings from 008), how, and from where. Whatever is
  decided, every attempted email must be logged (013 AC-6).

- **Other Status Codes** How should other status codes like 400 Bad Request be handled? Probably conditionally, if empty show something, if from a bad form validation show those errors...
