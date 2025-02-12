# Implementation Guide

1. **Copy the Code:**
   - Copy the code from [this GitHub repository](https://github.com/tristanhahn/shopify_gtm_custom_pixel/blob/main/custom_pixel) and paste it into a Shopify Custom Pixel.

2. **Add Event Listener Script:**
   - Add the following script immediately after the opening `<head>` tag:

     ```html
       <!--Event Listener for dataLayer Events from Custom Pixel-->
       <script>
       function handleCustomPixelEvent(event) {
         if (event.data.event_name === "gtm_custom_pixel_event" && event.data.json) {
           window.dataLayer = window.dataLayer || []; // Proper initialization
       
           try {
             const event_data = JSON.parse(event.data.json);
             console.log(event_data.ecommerce);
       
             if (event_data.ecommerce && Object.keys(event_data.ecommerce).length > 0) {
               window.dataLayer.push({ ecommerce: null });
             }
       
             window.dataLayer.push(event_data);
           } catch (error) {
             console.error("Error parsing JSON:", error);
           }
         }
       }   
       window.addEventListener('message', handleCustomPixelEvent); 
       </script>  
     ```

3. **Replace GTM Container Placeholder:**
   - Replace the GTM Container placeholder in the custom pixel code with your GTM Container ID.
  
4. **Configure Your GTM Container:**
   - Complete the configuration of your GTM Container as needed.
  
5. **Make sure that your GTM Container script is also placed into the theme.liquid:**
   - The Custom Pixel injects the GTM Container only into the checkout pages. For Non-checkout pages, you have to add the GTM Container snippet manually to the theme.liquid.

