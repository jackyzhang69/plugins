# What's new in 0.1.63

Opening a Windows local archive now stops as soon as the first working key is verified, then uses that key for the remaining databases. Typical setups finish sooner. If nothing is found, Windows gets one extra search pass; successful machines are not slowed down.
