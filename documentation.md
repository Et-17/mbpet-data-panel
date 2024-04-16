# Gæludýrasmájá

/ˈcaiːlʏˌtiːraˈsmauːˌsjauː/ \
Gæludýra = pet.PL.GEN \
Smájá = microscope.SG.NOM

## Structure

The website has two components: the panel, in the middle; and the buttons, in the bottom left corner.
The panel is a grid, composed of readouts.
There are three kinds of readouts: graph, state, and number.
These will be discussed in more detail later on, but simply the graph readout displayed a graph, the state readout displays a textual state, and the number readout displays a number with an optional unit.
Data is organized into *keys*, which are at most 8 ASCII characters.
Each readout is assigned a key, and will display the data from that key, and data is sent by the pets to a specific key.
Unfortunately, data can only be sent as a number, which is why the state readout exists: to map sent numbers into textual states.

For example, if you wanted to display a graph of a pet's compass heading and the amount of times that it has been fed,
you could make a graph readout attached to the key `COMPHEAD` and a number readout attached to the key `NUMFED`.
Then, on the pet's side, whenever you send the current heading to the key `COMPHEAD`, the new heading will be appended to the graph readout.
Usually, you would do this at regular intervals, but this is by no means required.
An whenever the pet gets fed, you send the new total feed count to `NUMFED`, and the number readout will automatically update to the most recently sent value.

### Graph Readout

I believe that the readout that will most commonly be used is the graph readout. This presents a graph of the last `max_data_points` values. After so many values are sent, it will remove the oldest one when new ones come in. The x-axis of the graph does not have any inherent meaning, as all received values are place at an equal distant from the other values, and if a team wants the values to be spaced according to time, it is their responsibility to send values at equal intervals.

The y-axis is autoranging by default, but you are able to specify a `max_value` and a `min_value`. Note that these two values are separate: it is possible to have the y-axis always locked at a specific minimum while the maximum is autoranging and vice versa.

### Number Readout

The number readout is the simplest of all the readouts: it simply displays the most recently sent value and will optionally append a unit. If you do not want a unit, then do not specify one. However, if you *do* want a unit, specify it as `unit`. Note that the space is not included, and if you want a space between the number and the unit, you must add it. For example: `" lb"`, not `"lb"`. This unit is fully Unicode compatible, and is therefore compatible with units such as Ohms (Ω) and Ångströms (Å), if you wanted those for whatever reason.

### State Readout

Sometimes you need to convy a text state, but you are only able to send numbers. There is a solution to this: the state readout, which will map the sent numbers to a text state. For example, if our pet could be in three states: happy, sad, and sleeping, then we could map 1 to happy, 2 to sad, and 3 to sleeping. Then if we send 1 to that channel, the state readout will display "happy"; if we send 2, the state readout will display "sad"; and so on. You are also able to specify a default state that will be displayed when the received number doesn't match anything. Like the number readout, the state readout is completely Unicode compliant, allowing you to use other scripts such as "счастливый" (Russian), "ความสุข" (Thai), or "高兴" (Mandarin) instead of "happy".

## The Panel Definition File

The configuration of your panel is determined by the panel definition file. It is a JSON file that contains the configuration of your panel. It is an object that contains three things: `rows`, `columns`, and `readouts`, where `rows` and `columns` are the amount of rows and columns in your panel respectively, and `readouts` is an array of readout objects. Going back to the earlier example, if we wanted that panel to have 2 rows and 3 columns, we could write the following.
```json
{
    "rows": 2,
    "columns": 3,
    "readouts": []
}
```
The file can be named whatever you like as long as it is a `.json` file. The next step is clearly adding the readouts, so we will discuss those next.

### Readouts

All readouts have common information that we will describe here, which is combined with type specific information. Each readout has a Unicode compliant `name`, which is diplayed at the top in big letters. Then you need to specify the `key`, which is displayed in smaller letters below the title. Next is `type`, which is `graph`, `state`, or `number`.

The hardestpart of this to understand is the position definition. `position` is an object containing four values: `cs`, `rs`, `ce`, and `re`.  `cs` (column start) is the column of the upper left corner, `rs` (row start) is the row of the upper left corner, `ce` (column end) is the column of the lower right corner, and `re` (row end) is the row of the lower right corner. It is planned to add more visual aid fto help explain positioning, but for now just guess and check if you can't figure it out.

#### Graph Readout