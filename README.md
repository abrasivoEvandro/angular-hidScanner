# Angularjs HID Barcode Scanner

This is a small module that can be used to read barcode scanner input. It works with scanners that act a keyboard devices. Its is inspired by [http://www.deadosaurus.com/](http://www.deadosaurus.com/detect-a-usb-barcode-scanner-with-javascript/); a jquery version of the same implementation.

## Features

* HID barcode scanner reader.
* Runs in global scope.
* Uses angulajs broadcast system to avoid local scope bindings.
* Implemented as a service so that the initialization is in the control of the application developer.

## How to use

```javascript
    factory('hidScanner', function($rootScope, $window, $timeout) {
        return {
            initialize : function() {
                var chars = [];
                var pressed = false;
                angular.element($window).on('keypress', function(e) {
                    if (e.which >= 48 && e.which <= 57) {
                        chars.push(String.fromCharCode(e.which));
                    }
                    // console.log(e.which + ":" + chars.join("|"));
                    if (pressed == false) {
                        $timeout(function(){
                            if (chars.length >= 10) {
                                var barcode = chars.join("");
                                $rootScope.$broadcast("hidScanner::scanned", {barcode: barcode});
                            }
                            chars = [];
                            pressed = false;
                        },250);
                    }
                    pressed = true;
                });
            }
        };
    })
```

Above code defined an angular service which listens to windows keypress events. The line

```javascript
    $rootScope.$broadcast("hidScanner::scanned", {barcode: barcode});
```

broadcasts the received barcode input.

```javascript
    $timeout(function(){
        if (chars.length >= 10) {
            var barcode = chars.join("");
            $rootScope.$broadcast("hidScanner::scanned", {barcode: barcode});
        }
        chars = [];
        pressed = false;
    },250);
```
Current timeout waits for 250 miliseconds. You can changes this value to match your scanner speed.

Add this module as dependency of your main app module:

```javascript
    angular.module('myAPp', ['angular-hidScanner'])
```

Pass the hidScanner service to you controller:

```javascript
    angular.controller('example', function ($scope, hIDScanner) {
        hIDScanner.initialize(); // initialze the keypress event listner
    });
```

## Dependencies:
[Angularjs](https://angularjs.org/)

## Authors
* Furqan Razzaq (furqan.razzaq@confiz.com)

Copyright::2014, Confiz. (http://www.confiz.com)
