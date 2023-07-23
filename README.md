# [<img src="https://ipinfo.io/static/ipinfo-small.svg" alt="IPinfo" width="24"/>](https://ipinfo.io/) IPinfo Java Client Library

[![License](http://img.shields.io/:license-apache-blue.svg)](LICENSE)

This is the official Java client library for the [IPinfo.io](https://ipinfo.io) IP address API, allowing you to lookup your own IP address, or get any of the following details for an IP:

 - [IP geolocation data](https://ipinfo.io/ip-geolocation-api) (city, region, country, postal code, latitude and longitude)
 - [ASN information](https://ipinfo.io/asn-api) (ISP or network operator, associated domain name, and type, such as business, hosting or company)
 - [Company data](https://ipinfo.io/ip-company-api) (the name and domain of the business that uses the IP address)
 - [Carrier details](https://ipinfo.io/ip-carrier-api) (the name of the mobile carrier and MNC and MCC for that carrier if the IP is used exclusively for mobile traffic)

Check all the data we have for your IP address [here](https://ipinfo.io/what-is-my-ip).

### Getting Started

You'll need an IPinfo API access token, which you can get by signing up for a free account at [https://ipinfo.io/signup](https://ipinfo.io/signup).

The free plan is limited to 50,000 requests per month, and doesn't include some of the data fields such as IP type and company data. To enable all the data fields and additional request volumes see [https://ipinfo.io/pricing](https://ipinfo.io/pricing)

[Click here to view the Java SDK's API documentation](https://ipinfo.github.io/java/).

#### Installation

##### Maven

Add these values to your pom.xml file:

Dependency:

```xml
<dependencies>
    <dependency>
        <groupId>io.ipinfo</groupId>
        <artifactId>ipinfo-api</artifactId>
        <version>2.1</version>
        <scope>compile</scope>
    </dependency>
</dependencies>
```

#### Quick Start

##### IP Information

````java
import io.ipinfo.api.IPinfo;
import io.ipinfo.api.errors.RateLimitedException;
import io.ipinfo.api.model.IPResponse;

public class Main {
    public static void main(String... args) {
        IPinfo ipInfo = new IPinfo.Builder()
                .setToken("YOUR TOKEN")
                .build();

        try {
            IPResponse response = ipInfo.lookupIP("8.8.8.8");

            // Print out the hostname
            System.out.println(response.getHostname());
        } catch (RateLimitedException ex) {
            // Handle rate limits here.
        }
    }
}
````

##### ASN Information

````java
import io.ipinfo.api.IPinfo;
import io.ipinfo.api.errors.RateLimitedException;
import io.ipinfo.api.model.IPResponse;

public class Main {
    public static void main(String... args) {
        IPinfo ipInfo = new IPinfo.Builder()
                .setToken("YOUR TOKEN")
                .build();

        try {
            ASNResponse response = ipInfo.lookupASN("AS7922");

            // Print out country name
            System.out.println(response.getCountry());
        } catch (RateLimitedException ex) {
            // Handle rate limits here.
        }
    }
}
````

#### Errors

- `ErrorResponseException`: A runtime exception accessible through the
  `ExecutionException` of a future. This exception signals that something went
  wrong when mapping the API response to the wrapper. You probably can't
  recover from this exception.
- `RateLimitedException` An exception signalling that you've been rate limited.

#### Caching

This library provides a very simple caching system accessible in `SimpleCache`.
Simple cache is an in memory caching system that resets every time you restart
your code.

If you prefer a different caching methodology, you may use the `Cache`
interface and implement your own caching system around your own infrastructure.

The default cache length is 1 day, this can be changed by calling the
SimpleCache constructor yourself.

```java
import io.ipinfo.api.IPinfo;
import io.ipinfo.api.errors.RateLimitedException;
import io.ipinfo.api.model.IPResponse;

public class Main {
    public static void main(String... args) {
        // 5 Day Cache
        IPinfo ipInfo = new IPinfo.Builder()
                .setToken("YOUR TOKEN")
                .setCache(new SimpleCache(Duration.ofDays(5)))
                .build();

        try {
            IPResponse response = ipInfo.lookupIP("8.8.8.8");

            // Print out the hostname
            System.out.println(response.getHostname());
        } catch (RateLimitedException ex) {
            // Handle rate limits here.
        }
    }
}
```

#### Country Name Lookup

This library provides a system to lookup country names through ISO2 country
codes.

By default, this translation exists for English (United States). If you wish to
provide a different language mapping, just use the following system in the
builder:

```java
import io.ipinfo.api.IPinfo;
import io.ipinfo.api.errors.RateLimitedException;
import io.ipinfo.api.model.IPResponse;

public class Main {
    public static void main(String... args) {
        IPinfo ipInfo = new IPinfo.Builder()
                .setToken("YOUR TOKEN")
                .setCountryFile(new File("path/to/file.json"))
                .build();

        try {
            IPResponse response = ipInfo.lookupIP("8.8.8.8");

            // Print out the country code
            System.out.println(response.getCountryCode());

            // Print out the country name
            System.out.println(response.getCountryName());
        } catch (RateLimitedException ex) {
            // Handle rate limits here.
        }
    }
}
```

This file must follow the same layout as seen [here](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/en_US.json)

More language files can be found [here](https://country.io/data)

#### EU Country Lookup

This library provides a system to lookup if a country is a member of the European Union (EU) through 
ISO2 country codes.

By default, [here](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/eu.json) is the file containing all the EU members. 
If you wish to provide a different file, just use the following system in the builder:

```java
import io.ipinfo.api.IPinfo;
import io.ipinfo.api.errors.RateLimitedException;
import io.ipinfo.api.model.IPResponse;

public class Main {
    public static void main(String... args) {
        IPinfo ipInfo = new IPinfo.Builder()
                .setToken("YOUR TOKEN")
                .setEUCountryFile(new File("path/to/file.json"))
                .build();

        try {
            IPResponse response = ipInfo.lookupIP("8.8.8.8");

            // Print out whether the country is a member of the EU
            System.out.println(response.isEU());
        } catch (RateLimitedException ex) {
            // Handle rate limits here.
        }
    }
}
```

#### Internationalization

This library provides a system to lookup if a country is a member of the European Union (EU), emoji and unicode of country's flag, code and symbol of country's currency, public link to the country's flag image as an SVG and continent code and name through ISO2 country codes.

Following are the file that are loaded by default:
- [eu.json](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/eu.json) 
- [flags.json](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/flags.json) 
- [currency.json](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/currency.json) 
- [continent.json](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/continent.json) 

If you wish to provide a different file, just use the following system in the builder:

```java
import io.ipinfo.api.IPinfo;
import io.ipinfo.api.errors.RateLimitedException;
import io.ipinfo.api.model.IPResponse;

public class Main {
    public static void main(String... args) {
        IPinfo ipInfo = new IPinfo.Builder()
                .setToken("YOUR TOKEN")
                .setEUCountryFile(new File("path/to/file.json"))
                .setCountryFlagFile(new File("path/to/file.json"))
                .setCountryCurrencyFile(new File("path/to/file.json"))
                .setContinentFile(new File("path/to/file.json"))
                .build();

        try {
            IPResponse response = ipInfo.lookupIP("8.8.8.8");

            // Print out whether the country is a member of the EU
            System.out.println(response.isEU());

            // CountryFlag{emoji='🇺🇸',unicode='U+1F1FA U+1F1F8'}
            System.out.println(response.getCountryFlag());

            // https://cdn.ipinfo.io/static/images/countries-flags/US.svg
            System.out.println(response.getCountryFlagURL());

            // CountryCurrency{code='USD',symbol='$'}
            System.out.println(response.getCountryCurrency());

            // Continent{code='NA',name='North America'}
            System.out.println(response.getContinent());
        } catch (RateLimitedException ex) {
            // Handle rate limits here.
        }
    }
}
```

The files must follow the same layout as seen 
- [eu.json](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/eu.json) 
- [flags.json](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/flags.json) 
- [currency.json](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/currency.json) 
- [continent.json](https://github.com/ipinfo/java-ipinfo/blob/master/src/main/resources/continent.json) 

#### Location Information

This library provides an easy way to get the latitude and longitude of an IP Address:

```java
import io.ipinfo.api.IPinfo;
import io.ipinfo.api.errors.RateLimitedException;
import io.ipinfo.api.model.IPResponse;

public class Main {
    public static void main(String... args) {
        IPinfo ipInfo = new IPinfo.Builder()
                .setToken("YOUR TOKEN")
                .setCountryFile(new File("path/to/file.json"))
                .build();

        try {
            IPResponse response = ipInfo.lookupIP("8.8.8.8");

            // Print out the Latitude and Longitude
            System.out.println(response.getLatitude());
            System.out.println(response.getLongitude());

        } catch (RateLimitedException ex) {
            // Handle rate limits here.
        }
    }
}
```

#### Extra Information

- This library is thread safe. Feel free to call the different endpoints from
  different threads.
- This library uses square's http client. Please refer to their documentation
  to get information on more functionality you can use.

### Other Libraries

There are official [IPinfo client libraries](https://ipinfo.io/developers/libraries) available for many languages including PHP, Python, Go, Java, Ruby, and many popular frameworks such as Django, Rails and Laravel. There are also many third party libraries and integrations available for our API.

### About IPinfo

Founded in 2013, IPinfo prides itself on being the most reliable, accurate, and in-depth source of IP address data available anywhere. We process terabytes of data to produce our custom IP geolocation, company, carrier, VPN detection, hosted domains, and IP type data sets. Our API handles over 40 billion requests a month for 100,000 businesses and developers.

[![image](https://avatars3.githubusercontent.com/u/15721521?s=128&u=7bb7dde5c4991335fb234e68a30971944abc6bf3&v=4)](https://ipinfo.io/)
