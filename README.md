<h1 align="center">kepubify</h1>

<a href="https://travis-ci.org/geek1011/kepubify"><img alt="Build Status" src="https://travis-ci.org/geek1011/kepubify.svg?branch=master" /></a>
<a href="https://goreportcard.com/report/github.com/geek1011/kepubify"><img alt="Go Report Card" src="https://goreportcard.com/badge/github.com/geek1011/kepubify" /></a>
<a href="https://godoc.org/github.com/geek1011/kepubify/kepub"><img alt="GoDoc" src="https://img.shields.io/badge/godoc-reference-blue.svg" /></a>

Kepubify converts EPUBs to KEPUBS. Kepubify also includes two standalone utilities
which do not depend on kepubify (and don't conflict with Calibre): [covergen](./covergen)
(which pre-generates cover images), and [seriesmeta](./seriesmeta) (which updates
Calibre or EPUB3 series metadata).

See the [releases](https://github.com/geek1011/kepubify/releases/latest) page for
download links, and the [website](https://pgaskin.net/kepubify) for more information.

## Usage
```
Usage: kepubify [OPTIONS] PATH [PATH]...

Options:
  -c, --css string                 custom CSS to add to ebook
      --fullscreen-reading-fixes   enable fullscreen reading bugfixes based on https://www.mobileread.com/forums/showpost.php?p=3113460&postcount=16
  -h, --help                       show this help text
      --hyphenate                  force enable hyphenation
      --inline-styles              inline all stylesheets (for working around certain bugs)
      --no-hyphenate               force disable hyphenation
  -o, --output string              the directory to place the converted files (default ".")
  -r, --replace stringArray        find and replace on all html files (repeat any number of times) (format: find|replace)
  -u, --update                     don't reconvert files which have already been converted
  -v, --verbose                    show extra information in output
      --version                    show the version

Arguments:
  PATH is the path to an epub file or directory to convert. If it is a directory,
  the converted dir is the name of the dir with the suffix _converted. If the path
  is a file, the converted file has the extension .kepub.epub.
```

## seriesmeta
Seriesmeta updates series metadata for sideloaded books. **New:** Seriesmeta now
supports updating metadata for unimported books, so you don't have to connect
twice (this is implemented using SQLite triggers). A reboot may be required in
some cases for the updated metadata to appear.

Seriesmeta works on EPUB and KEPUB books, and does not conflict with Calibre
(unless persistence is used, in which case seriesmeta will take precedence). It
will detect Calibre (`meta[name=calibre:series]`) and EPUB3
(`meta[property=belongs-to-collection]`) series metadata.

```
Usage: seriesmeta [OPTIONS] [KOBO_PATH]

Options:
  -h, --help         Show this help message
  -p, --no-persist   Don't ensure metadata is always set (this will cause series metadata to be lost if opening a book after an import but before a reboot)
  -n, --no-replace   Don't replace existing series metadata (you probably don't want this option)
  -u, --uninstall    Uninstall seriesmeta table and hooks (imported series metadata will be left untouched)

Arguments:
  KOBO_PATH is the path to the Kobo eReader. If not specified, seriesmeta will
  try to automatically detect the Kobo.
```

## covergen
Covergen (re)generates cover images for nickel, with optional stretching to fit
a specific aspect ratio (I use 1.5). This speeds up browsing the library, and if
stretching is used, will also make it more consistent. In addition, covergen is
useful when the automatically generated cover images are not satisfactory (too
small, white margins, etc).

Covergen works on EPUB and KEPUB books, and does not conflict with Calibre or any
other tool. It is also quite lenient about the way the cover image is referenced
by the book. The following methods are supported: `meta[name=cover]` with the path
as the content, `meta[name=cover]` with a manifest id reference as the content, and
`manifest>item[properties=cover-image]` with the image path as the href. Each
detected path can be relative to the epub root or to the package document.
Covergen does not support the external SD on older devices, and will ignore it.

The N3_LIBRARY_FULL, N3_LIBRARY_LIST, and N3_LIBRARY_GRID images are generated
using the same resizing algorithm as nickel (see [koboutils](https://github.com/geek1011/koboutils/blob/master/kobo/device.go) for more info).

```
Usage: covergen [OPTIONS] [KOBO_PATH]

Options:
  -a, --aspect-ratio float   Stretch the covers to fit a specific aspect ratio (for example 1.3, 1.5, 1.6)
  -h, --help                 Show this help message
  -m, --method string        Resize algorithm to use (bilinear, bicubic, lanczos2, lanczos3) (default "lanczos3")
  -r, --regenerate           Re-generate all covers

Arguments:
  KOBO_PATH is the path to the Kobo eReader. If not specified, covergen will try
  to automatically detect the Kobo.
```

