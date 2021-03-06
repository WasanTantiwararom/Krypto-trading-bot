ERR        = *** Unexpected MAKELEVEL = 0.
HINT       = This Makefile can't be used directly, consider cd ../../.. before try again
$(if $(subst 0,,${MAKELEVEL}),,$(warning $(ERR));$(error $(HINT)))
ERR        = *** Unexpected MAKE_VERSION < 4.
HINT       = This Makefile can't be used with your make version, consider to upgrade before try again
$(if $(shell ver=${MAKE_VERSION} && test $${ver%%.*} -lt 4 || echo 1),,$(warning $(ERR));$(error $(HINT)))

KASSETS    = /var/lib/K/assets
NODE_PATH  = /var/lib/K/node_modules
PATH      := $(NODE_PATH)/.bin:$(PATH)

STEPS     := $(shell grep "call\ STEP" Makefile | wc -l)
STEP       = $(shell tput setaf 2;tput setab 0)[$(1)/$(STEPS)]$(shell tput setaf 3;tput setab 0) Building client $(2)..$(shell tput sgr0)
STEP_1    := $(call STEP,1,npm packages)
STEP_2    := $(call STEP,2,ts files)
STEP_3    := $(call STEP,3,js zip file)
STEP_4    := $(call STEP,4,css files)

all $(KSRC): js css

npm: package.json
	$(info $(STEP_1))
	@cd $(abspath $(NODE_PATH)/..) && $@ install
ifndef TRAVIS_OS_NAME
	@rm $(abspath $(NODE_PATH)/..)/$(basename $^)*
endif

client: npm client/main.ts
	$(info $(STEP_2))
	NODE_PATH=$(NODE_PATH) tsc --baseUrl $(NODE_PATH) --alwaysStrict \
	  --experimentalDecorators -t ES2017 -m commonjs --outDir $(KASSETS)/www/js client/*.ts

js: client $(NODE_PATH)/.bin
	$(info $(STEP_3))
	NODE_PATH=$(NODE_PATH) browserify -t [ $(NODE_PATH)/babelify --presets [ $(NODE_PATH)/@babel/preset-env ] ] \
	  $(KASSETS)/www/js/main.js | uglifyjs | gzip > $(KASSETS)/www/js/client.min.js

css: www/sass/*.scss
	$(info $(STEP_4))
	$(foreach scss,$(wildcard www/sass/*.scss), \
	  sass $(scss) $(KASSETS)/www/css/$(basename $(notdir $(scss))).min.css ;)
	cat $(NODE_PATH)/@ag-grid-community/all-modules/dist/styles/ag-grid.css \
	  >> $(KASSETS)/www/css/bootstrap.min.css

export define PACKAGE_JSON
{
  "dependencies": {
    "@ag-grid-community/all-modules": "^22.1.1",
    "@ag-grid-community/angular":     "^22.1.2",
    "@angular/common":                   "^8.2.14",
    "@angular/compiler":                 "^8.2.14",
    "@angular/core":                     "^8.2.14",
    "@angular/forms":                    "^8.2.14",
    "@angular/platform-browser":         "^8.2.14",
    "@angular/platform-browser-dynamic": "^8.2.14",
    "@angular/router":                   "^8.2.14",
    "@babel/core":       "^7.8.4",
    "@babel/preset-env": "^7.8.4",
    "@types/node": "^13.7.0",
    "angular2-highcharts": "^0.5.5",
    "babelify": "^10.0.0",
    "browserify": "^16.5.0",
    "highcharts": "^8.0.0",
    "reflect-metadata": "^0.1.13",
    "rxjs": "6.5.4",
    "sass": "1.25.0",
    "typescript": "^3.7.5",
    "uglify-es": "^3.3.9",
    "zone.js": "^0.10.2"
  },
  "description": "K",
  "homepage": "https://github.com/ctubio/Krypto-trading-bot",
  "bugs": "https://github.com/ctubio/Krypto-trading-bot/issues",
  "repository": {
    "type": "git",
    "url": "git@github.com:ctubio/Krypto-trading-bot.git"
  },
  "author": "Michael Grosner",
  "contributors": [
    {
      "name": "Carles Tubio"
    }
  ],
  "os": [
    "darwin",
    "linux",
    "win32"
  ],
  "license": "(ISC OR MIT)"
}
endef

package.json:
	@echo "$$PACKAGE_JSON" > $(abspath $(NODE_PATH)/../$@)

.PHONY: all $(KSRC) client css js npm package.json
