# Stylish Flag

## Question Description
Webpage that only has the text: *Where is the flag*

## Solution
Went to sources through inspect and looked at css files where the flag was hidden through css art.
Used console command to reveal the flag
`` const f = document.querySelector('.flag');
f.hidden = false;
f.style.cssText = `
  position: fixed;
  left: 0;
  top: 0;
  transform: none;
  opacity: 1;
  background: #0f0;
  width: 8px;
  height: 8px;
  zoom: 0.25;
`;
document.body.style.background = "#000";
document.body.appendChild(f);
``
Had to adjust zoom aswell to make sure it fit in the page

## flag
v1t{H1D30UT_CSS}
