# Basta

![Asta Bastar](static/asta_bastar.jpg)

Basta (aka Binary Asta) customizable binary clock built using SvelteKit for the [Asta](https://github.com/stagrim/Asta) project

## Customization

### Flags
|url flag|default|usage|
|---|---|---|
|split|true|Chess board pattern between primary and secondary colors|
|numbers|true|Show numbers representing the value of active circles|
|show_inactive_numbers|false|Show numbers on inactive circles too when numbers are enabled|
|winter|false|Activate winter mode|
|true_binary_seconds|false|True binary mode for seconds row|

### Params
|url param|type|usage|
|---|---|---|
|info|number|Set type for info bubble (See [Info](#Info))|

#### Info
|value|Info type|
|---|---|
|1|Shows current day|
|2|Shows Analog clock|
