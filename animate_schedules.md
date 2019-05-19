Animating Age-Specific Demographic Schedules
================

First, download the data from [the Human Fertility Database](https://www.humanfertility.org). There is an `R` package, [HMDHFDplus](https://cran.r-project.org/web/packages/HMDHFDplus/index.html), that has utilities designed for use with these repositories, but I find it better pedagogically to go old-school when I'm doing this for a class.

Need to clean the ages a bit since under-12 is indicated as `12-` and over-55 by `55+`. Note that we have to double-escape the `+` in the regular expression since the plus sign is a quantifier for regular expressions.

``` r
mab <- read.table("USAmabRR.txt", header=TRUE, skip=2)
asfr <- read.table("USAasfrRR.txt", header=TRUE, skip=2)
# clean ages
asfr$Age <- gsub("55\\+", "55", as.character(asfr$Age))
asfr$Age <- as.numeric(gsub("12-", "12", as.character(asfr$Age)))
# helpful for iterating; also always good to check
(yrs <- unique(asfr$Year))
```

    ##  [1] 1933 1934 1935 1936 1937 1938 1939 1940 1941 1942 1943 1944 1945 1946
    ## [15] 1947 1948 1949 1950 1951 1952 1953 1954 1955 1956 1957 1958 1959 1960
    ## [29] 1961 1962 1963 1964 1965 1966 1967 1968 1969 1970 1971 1972 1973 1974
    ## [43] 1975 1976 1977 1978 1979 1980 1981 1982 1983 1984 1985 1986 1987 1988
    ## [57] 1989 1990 1991 1992 1993 1994 1995 1996 1997 1998 1999 2000 2001 2002
    ## [71] 2003 2004 2005 2006 2007 2008 2009 2010 2011 2012 2013 2014 2015 2016
    ## [85] 2017

``` r
# for scaling y-axis
max(asfr$ASFR)
```

    ## [1] 0.26932

``` r
# give it a test
plot(asfr$Age[asfr$Year==2017], asfr$ASFR[asfr$Year==2017], 
     type="l",
     lwd=3,
     col="blue3",
     xlab="Age", ylab="ASFR")
abline(v=mab$MAB[85], lty=2, col="red")
```

![](animate_schedules_files/figure-markdown_github/unnamed-chunk-1-1.png)

I use the `animate` package to create the gifs. Another option would have been `gganimate` which extends the `ggplot2` grammar of graphics to animated images, but I have more experience with `animate`, so that's what I went with. `animate` uses the [ImageMagick](http://www.imagemagick.org/script/index.php) library to create its animations, which may be a turn-off for some people since you need to install it separately. Fortunately, it's pretty straightforward to install using either [MacPorts](http://www.macports.org/) or [HomeBrew](https://brew.sh/).

Simply set up a `for` loop to generate the 85 ASFR curves within a `saveGIF()` command. I find that an interval of 0.3 seconds gives a pretty snappy but comprehensible. Anything much longer makes the animation seem ponderously slow. Any quicker and it's hard to relate the changing curves to the historical period.

``` r
library(animation)

saveGIF({ for(i in 1:85){
  plot(asfr$Age[asfr$Year==yrs[i]], asfr$ASFR[asfr$Year==yrs[i]], 
       type="l", 
       lwd=3,
       col="black",
       xlab="Age",
       ylab="ASFR",
       ylim=c(0,0.28))
  title(yrs[i])
  # mean age of childbearing
  abline(v=mab$MAB[i], lty=2, col="red")
}
  }, movie.name = "usa_asfr_1933-2017.gif", interval = 0.3)
```

    ## [1] TRUE

Unfortunately, the output doesn't display in a Markdown document, but I can insert it manually.

![animated asfr series](https://github.com/eehh-stanford/formal_demography/blob/master/usa_asfr_1933-2017.gif?raw=true)