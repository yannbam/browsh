browsh = browsh-root-directory

1. webext: 
cd webext
go install github.com/kevinburke/go-bindata@latest
../ctl.sh golang_lint_check // diff -u <(echo -n) <(gofmt -d ./) (should show nothing)
npm run lint

npm install
npx webpack
cd dist
web-ext build
web-ext run --verbose

cp web-ext-artifacts/browsh*.zip $browsh/interfacer/src/browsh/browsh.xpi

go-bindata -o $browsh/interfacer/src/browsh/webextension.go -pkg browsh $browsh/interfacer/src/browsh/browsh.xpi


cd $browsh/interfacer
go build -o browsh cmd/browsh/main.go



