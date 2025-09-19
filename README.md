# DEYELLOW-IMAGEMAGICK
DEYELLOW-USING-IMAGEMAGICK

```bash
for page in page*.png;
do
    echo -n "Processing ${page}...";
    magick $page -fuzz 20% -fill white -opaque "$(magick $page -format "%[pixel:p{10,10}]" info:)" -colorspace Gray -brightness-contrast -10x90 out/$page;
    echo done;
done
```
