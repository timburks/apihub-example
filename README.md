# apihub example

This repository contains a small example setup for using Apigee API hub.

## Setup instructions

Identify your project.
```
PROJECT=registry-eval
```
Configure the registry tool for your project.
This creates a `~/.config/registry` directory containing
files that configure the registry tool to point to your
server and project.
```
registry config configurations create hosted \
  --registry.address apigeeregistry.googleapis.com:443 \
  --registry.project $PROJECT
```
You can view your current configuration with `registry config list`
and you can make changes with `registry config set`.

Export the current contents of the registry.
This creates a directory named to match your `$PROJECT`
that contains the current contents of your registry.
```
registry export yaml projects/$PROJECT
```
Before adding APIs, temporarily disable background activity by removing
your manifest file. The manifest is essentially a Makefile that tells
the registry controller what generated artifacts you want in your registry
and how to generate them. Generated artifacts include API complexity metrics,
meta-properties like vocabularies, results of running linters, and API scores.
```
registry rpc delete-artifact \
  --name projects/$PROJECT/locations/global/artifacts/apihub-manifest
```
Now add the APIs from the `openapi-directory`.
```
registry upload bulk openapi ./openapi-directory/APIs
```

Re-export the registry with `registry export yaml`:
```
registry export yaml projects/$PROJECT
```
Now you have a directory of YAML files describing your registry in detail.
You can re-run this at any time and it will overwrite your current export
(but watch out: things that you delete from your registry won't be 
removed from your local files).

Make some changes to your registry and re-export to see what has changed.
You can make changes to your local files and push them to your registry
with `registry apply` like this:
```
registry apply -f $PROJECT -R
```

Here `$PROJECT` is the name of the top-level directory containing your
exported registry.





