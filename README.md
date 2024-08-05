Implementation Guide

1. Copy the code from https://github.com/tristanhahn/shopify_gtm_custom_pixel/blob/main/custom_pixel and paste it into a Shopify Custom Pixel.
2. Add this script immediately after the opening head tag:
     <!--Event Listener for dataLayer Events from Custom Pixel-->
    <script>
    function handleCustomPixelEvent(event) {
      if(event.data.event_name="gtm_custom_pixel_event" && event.data.json){
        window.dataLayer == window.dataLayer || [];  
        dataLayer.push(JSON.parse(event.data.json));    
      }
    }
    window.addEventListener('message', handleCustomPixelEvent); 
    </script>  
3. Replace the GTM Container placeholder in the code iof the custom pixel with your GTM Container ID.     
4. Configure your GTM Container.
