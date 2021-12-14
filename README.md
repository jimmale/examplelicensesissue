## example license issue

This is a small demo repo to illustrate an issue with [google/go-licenses](https://github.com/google/go-licenses)

### Showing what this simplified example is _supposed_ to do
```shell
go get github.com/jimmale/examplelicensesissue
cd $GOPATH/src/github.com/jimmale/examplelicensesissue/

# Manually storing the license for *this* repo. Ideally, go-licenses should be doing this for me. 
mkdir -p my-licenses/github.com/jimmale/examplelicensesissue/
cp ./LICENSE my-licenses/github.com/jimmale/examplelicensesissue/

# Manually storing the license for the logrus dependency. Ideally, go-licenses should be doing this for me. 
go mod vendor 
mkdir -p my-licenses/github.com/sirupsen/logrus/
cp ./vendor/github.com/sirupsen/logrus/LICENSE my-licenses/github.com/sirupsen/logrus/

# Build and run
go build .
./examplelicensesissue

# > [The licenses are printed to the terminal]

# Let's do some cleanup
rm -fr ./examplelicensesissue ./my-licenses ./vendor 
```

### The issue with go-licenses
```shell
# go get github.com/jimmale/examplelicensesissue
# cd $GOPATH/src/github.com/jimmale/examplelicensesissue/

# This fails because the my-licenses/ directory doesn't exist, and embed.FS requires that it exists
go-licenses save github.com/jimmale/examplelicensesissue --save_path="my-licenses/"

# > F1214 11:25:35.996285   45052 main.go:43] errors for ["github.com/jimmale/examplelicensesissue"]:
# > github.com/jimmale/examplelicensesissue: main.go:12:12: pattern my-licenses/*: no matching files found

# ... so let's create it. Let's also create an empty file; since an embed.FS cannot be empty.
mkdir my-licenses/
touch my-licenses/make_embedFS_happy

# Let's try again...
# This fails because the my-licenses/ directory already exists, and go-licenses doesn't like that
go-licenses save github.com/jimmale/examplelicensesissue --save_path="my-licenses/"

# > F1214 11:26:07.276510   45134 main.go:43] my-licenses/ already exists

# So now let's try the --force option...
go-licenses save github.com/jimmale/examplelicensesissue --force --save_path="my-licenses/"
# That also fails due to my-licenses/ not existing.

# > F1214 11:27:07.646418   45154 main.go:43] errors for ["github.com/jimmale/examplelicensesissue"]:
# > github.com/jimmale/examplelicensesissue: main.go:12:12: pattern my-licenses/*: no matching files found


# Why doesn't it exist? Because the --force option deletes the existing directory, 
# which leads us to the same same issue as the first example above. 
```