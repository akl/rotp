# ROTP - The Ruby One Time Password Library
[![Build Status](https://secure.travis-ci.org/mdp/rotp.png)](http://travis-ci.org/mdp/rotp)

A ruby library for generating one time passwords (HOTP & TOTP) according to [ RFC 4226 ](http://tools.ietf.org/html/rfc4226) and [ RFC 6238 ](http://tools.ietf.org/html/rfc6238)

This is compatible with Google Authenticator apps available for Android and iPhone, and now in use on GMail

## Quick overview of using One Time Passwords on your phone

* OTP's involve a shared secret, stored both on the phone and the server
* OTP's can be generated on a phone without internet connectivity (AT&T mode)
* OTP's should always be used as a second factor of authentication (if your phone is lost, your account is still secured with a password)
* Google Authenticator allows you to store multiple OTP secrets and provision those using a QR Code (no more typing in the secret)

## Dependencies

* OpenSSL

## Installation

    gem install rotp

## Use

### Time based OTP's

    totp = ROTP::TOTP.new("base32secret3232")
    totp.now # => "492039"

    # OTP verified for current time
    totp.verify("492039") # => true
    sleep 30
    totp.verify("492039") # => false

### Counter based OTP's

    hotp = ROTP::HOTP.new("base32secretkey3232")
    hotp.at(0) # => "260182"
    hotp.at(1) # => "055283"
    hotp.at(1401) # => "316439"

    # OTP verified with a counter
    hotp.verify("316439", 1401) # => true
    hotp.verify("316439", 1402) # => false

### Generating a Base32 Secret key

    ROTP::Base32.random_base32 # returns a 16 character base32 secret. Compatible with Google Authenticator

### Google Authenticator Compatible

The library works with the Google Authenticator iPhone and Android app, and also
includes the ability to generate provisioning URI's for use with the QR Code scanner
built into the app.

    totp.provisioning_uri("alice@google.com") # => 'otpauth://totp/alice@google.com?secret=JBSWY3DPEHPK3PXP'
    hotp.provisioning_uri("alice@google.com", 0) # => 'otpauth://hotp/alice@google.com?secret=JBSWY3DPEHPK3PXP&counter=0'

This can then be rendered as a QR Code which can then be scanned and added to the users
list of OTP credentials.

#### Working example

Scan the following barcode with your phone, using Google Authenticator

![QR Code for OTP](http://chart.apis.google.com/chart?cht=qr&chs=250x250&chl=otpauth%3A%2F%2Ftotp%2Falice%40google.com%3Fsecret%3DJBSWY3DPEHPK3PXP)

Now run the following and compare the output

    require 'rubygems'
    require 'rotp'
    totp = ROTP::TOTP.new("JBSWY3DPEHPK3PXP")
    p "Current OTP: #{totp.now}"

### Testing

    bundle install
    bundle exec rspec spec/*

### Contributors

    git shortlog -s -n

        47  Mark Percival
        6  David Vrensk
        1  Guillaume Rose
        1  Micah Gates
        1  Michael Brodhead & Shai Rosenfeld
        1  Nathan Reynolds
        1  Shai Rosenfeld
        1  Shai Rosenfeld & Michael Brodhead

### Changelog


#### 2.0.0

- Move to only comparing string OTP's.


#### 1.7.0

- Move to only comparing string OTP's. See mdp/rotp/issues/32 - Moved to 2.0.0 - yanked from RubyGems

#### 1.6.1

- Remove deprecation warning in Ruby 2.1.0 (@ylansegal)
- Add Ruby 2.0 and 2.1 to Travis

#### 1.6.0

- Add verify_with_retries to HOTP
- Fix 'cgi' require and global DEFAULT_INTERVAL

#### 1.5.0

- Add support for "issuer" parameter on provisioning url
- Add support for "period/interval" parameter on provisioning url

#### 1.4.6

- Revert to previous Base32

#### 1.4.5

- Fix and test correct implementation of Base32

#### 1.4.4

- Fix issue with base32 decoding of strings in a length that's not a multiple of 8

#### 1.4.3

- Bugfix on padding

#### 1.4.2

- Better padding options (Pad the output with leading 0's)

#### 1.4.1

- Clean up drift logic

#### 1.4.0

- Added clock drift support via 'verify_with_drift' for TOTP

####1.3.0

- Added support for Ruby 1.9.x
- Removed dependency on Base32

### License

MIT Licensed

### See also:

Python port PYOTP by [Nathan Reynolds](https://github.com/nathforge) - [https://github.com/nathforge/pyotp](https://github.com/nathforge/pyotp)  
PHP port OTPHP by [Le Lag](https://github.com/lelag) - [https://github.com/lelag/otphp](https://github.com/lelag/otphp)
