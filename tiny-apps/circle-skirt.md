---
layout: muicontent
group: tiny-apps
---

# Circle Skirt Calculator



## Parameters

<form>

<p>Waist Circumference: <input type="text" id="waist_circumference"/> (cm)</p>

<p>Fullness of Skirt: <select id="skirt_fullness"><option value="4">1/4 Circle</option><option value="3">1/3 Circle</option><option value="2">1/2 Circle</option><option value="1.333333333333">3/4 Circle</option><option value="1">Full Circle</option></select></p>

<p>Number of Panels: <input type="number" min="1" max="8" value="2" id="number_of_panels"/> (1-8)</p>

<p>Seam Allowance: <input type="text" id="seam_allowance" value="1"/> (cm)</p>

<p>(Optional) Skirt Length: <input type="text" id="skirt_length"/> (cm)</p>

<p><button onclick="calculate_circle_skirt(); return false" type="button">Calculate!</button></p>

</form>

## Calculations

`type_of_skirt_ratio = 1 / skirt_fullness //` <input type="text" disabled id="_type_of_skirt_ratio"/>

`equivalent_circle = type_of_skirt_ratio * waist_circumference //` <input type="text" disabled id="_equivalent_circle"/>

`number_of_seams = 2 * number_of_panels //` <input type="text" disabled id="_number_of_seams"/>

`total_seam_allowances = number_of_seams * seam_allowance //` <input type="text" disabled id="_total_seam_allowances"/>

`adjusted_circumference = equivalent_circle + total_seam_allowance //` <input type="text" disabled id="_adjusted_circumference"/>

`inner_radius = adjusted_circumference / (2 * pi) //` <input type="text" disabled id="_inner_radius"/>

`adjusted_inner_radius = inner_radius - seam_allowance //` <input type="text" disabled id="_adjusted_inner_radius"/>

`angle_of_panel = (skirt_fullness * 2 * pi) / number_of_panels //` <input type="text" disabled id="_angle_of_panel"/> (radians)

`required_width = (skirt_length + adjusted_inner_radius) * sin(angle_of_panel) //` <input type="text" disabled id="_required_width"/>

## Result

Cut out an inner radius of <input type="text" disabled id="_resultant"/> (cm)

At an inner angle of <input type="text" disabled id="_resangle_degrees"/> (degrees)

(Optional) You will need fabric which has a width of at least <input type="text" disabled id="_need_width"/> (cm)

The width of a bolt of fabric tends to be in the range between 89cm and 150cm (35in and 60in). [https://en.wikipedia.org/wiki/Bolt_(cloth)](https://en.wikipedia.org/wiki/Bolt_(cloth))


<script>
function calculate_circle_skirt() {
   type_of_skirt_ratio = skirt_fullness.value * 1
      _type_of_skirt_ratio.value = type_of_skirt_ratio
   equivalent_circle = type_of_skirt_ratio * waist_circumference.value
      _equivalent_circle.value = equivalent_circle
   number_of_seams = 2 * number_of_panels.value
      _number_of_seams.value = number_of_seams
   total_seam_allowances = number_of_seams * seam_allowance.value
      _total_seam_allowances.value = total_seam_allowances
   adjusted_circumference = equivalent_circle + total_seam_allowances
      _adjusted_circumference.value = adjusted_circumference
   inner_radius = adjusted_circumference / (2 * Math.PI)
      _inner_radius.value = inner_radius
   adjusted_inner_radius = inner_radius - seam_allowance.value
      _adjusted_inner_radius.value = adjusted_inner_radius
      _resultant.value = adjusted_inner_radius
   angle_of_panel = (1 / skirt_fullness.value) * 2 * Math.PI / number_of_panels.value
      _angle_of_panel.value = angle_of_panel
      //_resangle.value = angle_of_panel
      _resangle_degrees.value = + (angle_of_panel * (180/Math.PI)).toFixed(4)

   // optional
   if (skirt_length.value != '') {
      if (angle_of_panel <= (Math.PI / 2)) {
         required_width = (skirt_length.value*1 + adjusted_inner_radius) * Math.sin(angle_of_panel)
      } else {
         required_width = (skirt_length.value * 1 + adjusted_inner_radius)
      }
         _required_width.value = required_width
         _need_width.value = required_width
   } else {
         _required_width.value = ''
         _need_width.value = ''
   }
}
</script>
