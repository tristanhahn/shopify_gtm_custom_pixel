Add this script immediately after the opening head tag:

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
