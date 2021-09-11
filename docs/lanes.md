# Lanes

Lanes.pcvd is organized into three tabs based on the number of indications in a lane. All single-use lane configurations are included, and the most common dual-use and triple-use configurations are also included.

Each canvas is named according to the format `lane a or b or c using d`, where _a_, _b_, and _c_ are indications and _d_ is a valid indication matching one of the other three indications. _b_ and _c_ are only present in some of the canvas names.

## Translating lane indications to a canvas

This naming scheme assumes the following algorithm for translating the Mapbox Directions API’s [lane objects](https://docs.mapbox.com/api/navigation/directions/#lane-object)’ `indications` array to a canvas:

1. Classify each indication as leftward or rightward:
   * If the array contains both leftward and rightward indications:
      * If `valid_indication` is leftward, prepend `opposite ` to any rightward indication and set the `flipped` flag.
      * If `valid_indication` is rightward, prepend `opposite ` to any leftward indication.
   * Otherwise, if the array contains a leftward indication, set the `flipped` flag.
1. For each indication, replace any occurrence of `left` or `right` with `turn`.
1. If `valid_indication` is set, replace any occurrence of `left` or `right` with `turn`.
1. If `valid_indication` is unset, set `primary_color` to the same value as `secondary_color`.
1. Sort the array of indications in the following order: `opposite uturn`, `opposite sharp turn`, `opposite turn`, `opposite slight turn`, `straight`, `slight turn`, `turn`, `sharp turn`, `uturn`.
1. If there are more than three indications, keep one that matches `valid_indication` and two others and discard the rest.
1. Build the canvas name by concatenating the following strings:
   1. `lane `
   1. the array of indications joined by ` or `
   1. ` using `
   1. the value of `valid_indication` or, if it is unset, the first indication
1. Look up the canvas by name:
   * If the canvas exists, draw it using the `primary_color` and `secondary_color`.
   * Otherwise, if it does not exist:
      * If `valid_indication` is set, draw the canvas with that name using the `primary_color`.
      * Otherwise, if `valid_indication` is unset, draw the canvas `straight` using the `secondary_color`.
1. If the `flipped` flag is set, flip the image horizontally.

In the algorithm above, “leftward” means `sharp left`, `left`, or `slight left`; “rightward” means `slight right`, `right`, or `sharp_right`. `uturn` is leftward if the driving side is to the right and rightward if the driving side is to the left.
