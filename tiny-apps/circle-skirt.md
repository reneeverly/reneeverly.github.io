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

## (Optional) Suggested Pattern Layout

This layout required fabric length of <input type="text" disabled id="_req_length"/> (cm)

<canvas id="fabricmap" height="200" width="200" style="max-width: 100%;"></canvas>

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
      panel_length = (skirt_length.value * 1 + adjusted_inner_radius)
      if (angle_of_panel <= (Math.PI / 2)) {
         // less than 90 degrees, two panels per section
         required_width = panel_length * Math.sin(angle_of_panel)
         num_panels_per_section = 2
         overflow_per_section = panel_length - panel_length * Math.cos(angle_of_panel)
         adjust_midpoint = 0
      } else if (angle_of_panel > Math.PI) {
         // more than 180 degrees, only one panel and super wide
         required_width = panel_length * 2
         num_panels_per_section = 1
         overflow_per_section = panel_length
         adjust_midpoint = 100
      } else {
         // somwhere between 90 and 180 degrees, single panel per section
         required_width = panel_length
         num_panels_per_section = 1
         overflow_per_section = panel_length * Math.cos(Math.PI - angle_of_panel)
         adjust_midpoint = 0
      }
         _required_width.value = required_width
         _need_width.value = required_width

      var unit = required_width / 200
      fabricmap.width = Math.ceil((panel_length + overflow_per_section) * Math.ceil(number_of_panels.value / num_panels_per_section) / unit)
         _req_length.value = fabricmap.width * unit
      let ctx = fabricmap.getContext('2d')
      ctx.fillStyle = 'white'
      ctx.fillRect(0, 0, fabricmap.width, 200)


      // iterate through, filling in the panels
      let progx = 0
      for (var i = 1; i <= (number_of_panels.value * 1); i++) {
         if (num_panels_per_section == 1) {
            draw_panel(ctx, (panel_length * i + overflow_per_section * (i - 1)) / unit, adjust_midpoint, true)
         } else {
            if (i % 2 == 0) {
               // special case for goes right
               progx -= panel_length / unit
               progx += overflow_per_section / unit
               draw_panel(ctx, progx, required_width / unit, false)
               progx += panel_length / unit
            } else {
               // goes right
               progx += panel_length / unit
               draw_panel(ctx, progx, 0, true)
            }
         }
      }
   } else {
         _required_width.value = ''
         _need_width.value = ''
         _req_length.value = ''

         fabricmap.width = 200
         let ctx = fabricmap.getContext('2d')
         ctx.fillStyle = 'white'
         ctx.fillRect(0, 0, 100, 100)
   }

   function draw_panel(ctx, originx, originy, orientation) {
      console.log(ctx, originx, originy, orientation)
      console.log(unit)
      ctx.beginPath()
      ctx.moveTo(originx, originy)
      if (orientation) { // true = left
         ctx.strokeStyle= '#000000'
         ctx.lineTo(originx - (panel_length / unit), originy)
         console.log("LINE FROM: ", originx, originy, " LINE TO: ", originx - (panel_length / unit), originy)
         ctx.arc(originx, originy, panel_length / unit, Math.PI, Math.PI - angle_of_panel, true)
         ctx.moveTo(originx - (adjusted_inner_radius / unit), originy)
         ctx.arc(originx, originy, adjusted_inner_radius / unit, Math.PI, Math.PI - angle_of_panel, true)
         ctx.moveTo(originx, originy)
         ctx.lineTo(originx - (panel_length * Math.cos(angle_of_panel)) / unit, originy + (panel_length * Math.sin(angle_of_panel)) / unit)
      } else {
         ctx.strokeStyle= '#000000'
         ctx.lineTo(originx + (panel_length / unit), originy)
         console.log("LINE FROM: ", originx, originy, " LINE TO: ", originx + (panel_length / unit), originy)
         ctx.arc(originx, originy, panel_length / unit, 0, -angle_of_panel, true)
         ctx.moveTo(originx + (adjusted_inner_radius / unit), originy)
         ctx.arc(originx, originy, adjusted_inner_radius / unit, 0, -angle_of_panel, true)
         ctx.moveTo(originx, originy)
         ctx.lineTo(originx + (panel_length * Math.cos(angle_of_panel)) / unit, originy - (panel_length * Math.sin(angle_of_panel)) / unit)
      }
      ctx.stroke()
   }
}
</script>
