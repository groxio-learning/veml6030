# VEML6030

This project wraps the VEML6030 ambient light sensor, using the Nerves [Circuits.I2C](https://github.com/elixir-circuits/circuits_i2c) library. It has a basic GenServer wrapper that polls every second for a measurement, and returns the last measurement via a call to `VEML6060.measure/0`. 

## Installation

If [available in Hex](https://hex.pm/docs/publish), the package can be installed
by adding `veml6030` to your list of dependencies in `mix.exs`:

```elixir
def deps do
  [
    {:veml6030, "~> 0.1.0"}
  ]
end
```

## Usage 

There are two main public APIs. One to start, and one to measure. 

### Configure and Start

If you have not yet determined the configuration for your sensor, start it with: 

```elixir
iex> {:ok, _pid} = VEML.start_link(%{})
iex> VEML.measure
%{}
```

This command will log a warning, but tell you where it detected the device. It's preferred to start the device with the i2c bus name and address, like this: 

```elixir
iex> VEML.start_link(%{address: i2c_address, i2c_bus_name: i2c_bus_name})
```

To discover the address and bus name, shell into your device and run discovery: 

```elixir
iex> {bus_name, device_address} = Circuits.I2C.discover_one!([0x48, 0x10])
```

You can also set the gain and integration time (like a shutter speed) to the light sensor to adjust the sensor to your needs. 

### Setting the Gain and Integration Time

To set the gain, pass the  `:gain` key to your start link, with one of these resolution values: `[:gain_2x, :gain_1x, :gain_1_4th, :gain_1_8th]`. To set the integration time, pass the `:int_time` key with one of `[:it_25_ms, :it_50_ms, :it_100_ms, :it_200_ms, :it_400_ms, :it_800_ms]`, like this: 

```elixir
iex> VEML.start_link(%{gain: :gain_1_4th, int_time: :it_200_ms})
```

The `:shutdown` and `:interrupt` keys are correct in terms of the VEML6030 data sheet, but have not been tested. 

If you want to own this project and post it to Hex, your stewardship would be most welcome. 