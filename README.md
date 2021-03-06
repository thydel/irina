<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Inaturalist](#inaturalist)
    - [TL;DR](#tldr)
    - [Query API for androctonus bicolor](#query-api-for-androctonus-bicolor)
    - [Build a shell script from this of all image URL](#build-a-shell-script-from-this-of-all-image-url)
    - [Find location ID for a continent](#find-location-id-for-a-continent)

<!-- markdown-toc end -->

# Inaturalist

## TL;DR

The [images](Androctonus-bicolor.md)

## Query API for androctonus bicolor

```bash
curl -s 'https://api.inaturalist.org/v1/search?q=androctonus%20bicolor' | jq > androctonus-bicolor.json
```

- [androctonus-bicolor.json](androctonus-bicolor.json)

## Build a shell script from this of all image URL

As all image have the same basename we must generate a new name using ID

```bash
jq='.results[0].record | (.name | sub(" "; "-"; "g")) as $name | .taxon_photos[].photo | "curl -so \($name)-\(.id).jpg \(.original_url)"'
```

The generated script

```console
thy@tde-ws:~/usr/hub/work/ext/irina$ jq -r "$jq" androctonus-bicolor.json
curl -so Androctonus-bicolor-95813663.jpg https://static.inaturalist.org/photos/95813663/original.jpg
curl -so Androctonus-bicolor-91893919.jpg https://static.inaturalist.org/photos/91893919/original.jpg
curl -so Androctonus-bicolor-18915044.jpg https://static.inaturalist.org/photos/18915044/original.jpg
curl -so Androctonus-bicolor-91893920.jpg https://static.inaturalist.org/photos/91893920/original.jpg
curl -so Androctonus-bicolor-9548340.jpg https://static.inaturalist.org/photos/9548340/original.jpg
curl -so Androctonus-bicolor-18915047.jpg https://static.inaturalist.org/photos/18915047/original.jpg
curl -so Androctonus-bicolor-107142893.jpg https://inaturalist-open-data.s3.amazonaws.com/photos/107142893/original.jpg
```

Use the script

```bash
jq -r "$jq" androctonus-bicolor.json | dash
```

Generate a page to show images

```bash
ls *.jpg | jq -Rr 'split(".")[0] | "- ![\(.)](\(.).jpg)"' > Androctonus-bicolor.md
```

- [Androctonus-bicolor.md](Androctonus-bicolor.md)

## Find location ID for a continent

- Found [Is there a place where I can go to get a list of iNaturalist place_ids?][] on `forum.inaturalist.org`
- Then, from [API Reference][]

```bash
curl -s https://www.inaturalist.org/places.json?place_type=continent | jq > place_type-continent.json
```

- [place_type-continent.json](place_type-continent.json)

And a simple `bash` func

```bash
get-continent-id () { jq --arg continent ${1:?} '.[] | select(.name == $continent) | .id' place_type-continent.json; }
```

To augment the request URL with Asia `place_id`

```console
thy@tde-ws:~/usr/hub/work/ext/irina$ curl -s "https://api.inaturalist.org/v1/search?q=androctonus%20bicolor&place_id=$(get-continent-id Asia)" | jq .total_results
0
```

But, zero match

[Is there a place where I can go to get a list of iNaturalist place_ids?]:
    https://forum.inaturalist.org/t/is-there-a-place-where-i-can-go-to-get-a-list-of-inaturalist-place-ids/4016/3
    "forum.inaturalist.org"

[API Reference]:
    https://www.inaturalist.org/pages/api+reference#get-places
    "inaturalist.org"

[Local Variables:]::
[indent-tabs-mode: nil]::
[End:]::
