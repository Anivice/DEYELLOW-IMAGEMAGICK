# DEYELLOW-IMAGEMAGICK

## Deyellow using imagemagick:

```bash
for page in page*.png;
do
    echo -n "Processing ${page}...";
    magick $page -fuzz 20% -fill white -opaque "$(magick $page -format "%[pixel:p{10,10}]" info:)" -colorspace Gray -brightness-contrast -10x90 out/$page;
    echo done;
done
```

## Check for non-white pixel areas larger than 10x10 and find upper and lower bound of them in a clean image

```bash
in=page.png
H="$(identify "$in" | sed -E 's/.*\s([0-9]+)x([0-9]+)\s.*/\2/g')"
magick "$in" -colorspace sRGB -color-threshold "rgb(245,245,245)-rgb(255,255,255)" -negate -define connected-components:area-threshold=100 -define connected-components:verbose=true -connected-components 8 null: | awk '
  $1 ~ /^[0-9]+:$/ {
    bbox=$2; mean=$5
    if (mean!="srgb(255,255,255)") next
    split(bbox,bb,/x|\+/); w=bb[1]; h=bb[2]; x=bb[3]; y=bb[4]
    if (w>=10 && h>=10) {
      end=y+h-1
      if (!seen++ || y<top) top=y
      if (!seen2++ || end>bottom) bottom=end
    }
  }
  END {
    if (seen) printf("top_row=%d\nbottom_row=%d\n", top, bottom);
    else printf("top_row=-1\nbottom_row=-1\n");
  }'
```
