/*
Copyright: Tristan Hahn
https://github.com/tristanhahn/shopify_gtm_custom_pixel
*/

let customerPrivacyStatus = init.customerPrivacy;

api.customerPrivacy.subscribe('visitorConsentCollected', (event) => {
  customerPrivacyStatus = event.customerPrivacy;
  var event_details = {
      event:"shopify_consent",
      consent_marketing: customerPrivacyStatus.marketingAllowed,
      consent_analytics: customerPrivacyStatus.analyticsProcessingAllowed,
      consent_preferences: customerPrivacyStatus.preferencesProcessingAllowed,
  };
  parent.postMessage({
      'event_name': 'gtm_custom_pixel_event', 
      'json': JSON.stringify({ ...event_details}) 
  }, init.context.document.location.origin);
});

var GTM_LOADED=0;

function loadGTM(){
  window.dataLayer = window.dataLayer || [];
  
  window.dataLayer.push({
	event: 'gtm_checkout_init',
	gtm_loaded_from: 'custom_pixel'
  });
   
  (function(w, d, s, l, i) {
	w[l] = w[l] || [];
	w[l].push({
		'gtm.start': new Date().getTime(),
		event: 'gtm.js'
	});
	var f = d.getElementsByTagName(s)[0],
		j = d.createElement(s),
		dl = l != 'dataLayer' ? '&l=' + l : '';
	j.async = true;
	j.src = 'https://www.googletagmanager.com/gtm.js?id=' + i + dl;
	f.parentNode.insertBefore(j, f);
  })(window, document, 'script', 'dataLayer', 'GTM-XXXX');
  
  GTM_LOADED = 1;
  
}

function pushToDataLayer(eventType, eventDetails, ecommerceDetails, customerDetails, pageDetails) {
    window.dataLayer = window.dataLayer || [];
  
    window.dataLayer.push({
        event: eventType,
        ...eventDetails,
        ecommerce: ecommerceDetails,
        customer: customerDetails,
        fired_from: 'custom_pixel',
        page_details: pageDetails
    });
}
	
function sendToParent(eventType, eventDetails, ecommerceDetails, customerDetails, pageDetails) {

   event_data = {
        event: eventType,
        ...eventDetails,
        ecommerce: ecommerceDetails,
        customer: customerDetails,
        fired_from: 'custom_pixel',
        page_details: pageDetails
    };
    
    parent.postMessage({
		'event_name': 'gtm_custom_pixel_event', 
		'json': JSON.stringify(event_data) 
	}, pageDetails.page_origin);
}

const commonCustomerDetails = (event, init) => ({
    id: init.data.customer?.id ?? undefined,
    client_id: event.clientId,
    email: init.data.customer?.email ?? undefined,
    phone: init.data.customer?.phone ?? undefined,
    first_name: init.data.customer?.firstName ?? undefined,
    last_name: init.data.customer?.lastName ?? undefined,
    shop_country_code: init.data.shop?.countryCode ?? undefined,
});

var pageDetails = (context) => ({
    page_url: context.document.location?.href ?? undefined,
    store_front_url: init.data.shop.storefrontUrl,
    page_title: context.document?.title ?? undefined,
    page_referrer: context.document?.referrer ? context.document.referrer : undefined,
    page_origin: context.document?.location.origin ? context.document.location.origin : undefined,
});


analytics.subscribe('page_viewed', (event) => {
    sendToParent('gtm_page_viewed', {
        event_id: event.id,
    }, {
    }, commonCustomerDetails(event, init), pageDetails(event.context)
    );
});


analytics.subscribe('collection_viewed', (event) => {
    sendToParent('gtm_view_item_list', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.collection.productVariants[0].price.currencyCode,
        item_list_name: event.data.collection.title,
        item_list_id: event.data.collection.id,
        items: event.data.collection.productVariants.map((item, index) => ({
            item_brand: item.product.vendor,
            item_category: item.product.type,
            item_id: item.sku === '' ? item.id : item.sku,
            image: item.image.src,
            variant_id: item.id,
            variant: item.title,
            product_id: item.product.id,
            item_name: item.product.title,
            price: item.price.amount,
            index: index,
        }))
    }, commonCustomerDetails(event, init), pageDetails(event.context)
    );
});

analytics.subscribe('search_submitted', (event,init) => {
    sendToParent('gtm_view_search_result', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp,
        search_term: event.data.searchResult.query
    }, {
        currency: event.data.searchResult.productVariants[0].price.currencyCode,
        item_list_name: 'search_result',
        item_list_id: 'search_result',
        items: event.data.searchResult.productVariants.map((item, index) => ({
            item_brand: item.product.vendor,
            item_category: item.product.type,         
            item_id: item.sku === '' ? item.id : item.sku,
            image: item.image.src,
            variant_id: item.id,
            variant: item.title,
            product_id: item.product.id,
            item_name: item.product.title,
            price: item.price.amount,
            index: index
        }))
    }, commonCustomerDetails(event, init), pageDetails(event.context)
    );
});

analytics.subscribe('product_viewed', (event) => {
    sendToParent('gtm_view_item', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.productVariant.price.currencyCode,
        value: event.data.productVariant.price.amount,
        items: [{
            item_category: event.data.productVariant.product.type,
            item_brand: event.data.productVariant.product.vendor,
            item_id: event.data.productVariant.sku === '' ? event.data.productVariant.id : event.data.productVariant.sku,
            image: event.data.productVariant.image.src,
            variant_id: event.data.productVariant.id,
            variant: event.data.productVariant.title,
            product_id: event.data.productVariant.product.id,
            item_name: event.data.productVariant.product.title,
            price: event.data.productVariant.price.amount
        }]
    }, commonCustomerDetails(event, init), pageDetails(event.context)
    );
});

analytics.subscribe('product_added_to_cart', (event) => {
    sendToParent('gtm_add_to_cart', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.cartLine.merchandise.price.currencyCode,
        value: event.data.cartLine.merchandise.price.amount,
        items: [{
            item_category: event.data.cartLine.merchandise.product.type,
            item_brand: event.data.cartLine.merchandise.product.vendor,
            item_id: event.data.cartLine.merchandise.sku === '' ? event.data.cartLine.merchandise.id : event.data.cartLine.merchandise.sku,
            image: event.data.cartLine.merchandise.image.src,
            variant_id: event.data.cartLine.merchandise.id,
            variant: event.data.cartLine.merchandise.title,
            product_id: event.data.cartLine.merchandise.product.id,
            item_name: event.data.cartLine.merchandise.product.title,
            price: event.data.cartLine.merchandise.price.amount,
            quantity: event.data.cartLine.quantity
        }]
    }, commonCustomerDetails(event, init), pageDetails(event.context)
    );
});

analytics.subscribe('product_removed_from_cart', async (event) => {
    sendToParent('gtm_remove_from_cart', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.cartLine.merchandise.price.currencyCode,
        value: event.data.cartLine.merchandise.price.amount,
        items: [{
            item_category: event.data.cartLine.merchandise.product.type,
            item_brand: event.data.cartLine.merchandise.product.vendor,
            item_id: event.data.cartLine.merchandise.sku === '' ? event.data.cartLine.merchandise.id : event.data.cartLine.merchandise.sku,
            image: event.data.cartLine.merchandise.image.src,
            variant_id: event.data.cartLine.merchandise.id,
            variant: event.data.cartLine.merchandise.title,
            product_id: event.data.cartLine.merchandise.product.id,
            item_name: event.data.cartLine.merchandise.product.title,
            price: event.data.cartLine.merchandise.price.amount,
            quantity: event.data.cartLine.quantity
        }]
    }, commonCustomerDetails(event, init), pageDetails(event.context)
    );
});

analytics.subscribe('cart_viewed', async (event) => {
    sendToParent('gtm_view_cart', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.cart.cost.totalAmount.currencyCode,
        value: event.data.cart.cost.totalAmount.amount,
        cart_id: event.data.cart.id,
        items: event.data.cart.lines.map(item => ({
            item_brand: item.merchandise.product.vendor,
            item_category: item.merchandise.product.type,
            item_id: item.merchandise.sku === '' ? item.merchandise.id : item.merchandise.sku,
            image: item.merchandise.image.src,
            variant_id: item.merchandise.id,
            variant: item.merchandise.title,
            product_id: item.merchandise.product.id,
            item_name: item.merchandise.product.title,
            quantity: item.quantity,
            price: item.merchandise.price.amount
        }))},commonCustomerDetails(event, init),pageDetails(event.context)
    ); 
});

analytics.subscribe('checkout_started', (event) => {

	if(GTM_LOADED == 0){
			loadGTM();
	}
	
    pushToDataLayer('gtm_begin_checkout', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.checkout.currencyCode,
        value: event.data.checkout.totalPrice.amount,
        tax: event.data.checkout.totalTax.amount,
        shipping: event.data.checkout.shippingLine.price.amount,
        sub_total: event.data.checkout.subtotalPrice.amount,
        checkout_token: event.data.checkout.token,
        coupon: event.data.checkout.discountApplications?.find(app => app.type === "DISCOUNT_CODE")?.title ?? undefined,
        items: event.data.checkout.lineItems.map(item => ({
            item_brand: item.variant.product.vendor,
            item_id: item.variant.sku === '' ? item.variant.id : item.variant.sku,
            item_category: item.variant?.product?.type ?? undefined,
            image: item.variant.image.src,
            variant_id: item.variant.id,
            variant: item.variant.title,
            product_id: item.variant.product.id,
            item_name: item.title,
            quantity: item.quantity,
            price: item.variant.price.amount,
            coupon: (item.discountApplications && item.discountApplications.length > 0) ? item.discountApplications[0].title : undefined,
            discount: (item.discountApplications && item.discountApplications.length > 0) ? item.discountApplications[0].amount.amount : undefined,
        }))
    },{
		id: init.data.customer?.id ?? undefined,
		client_id: event.clientId,
		email: init.data.customer?.email || event.data.checkout?.email || undefined,
		phone: init.data.customer?.phone || event.data.checkout?.phone || undefined,
		first_name: init.data.customer?.first_name || event.data.checkout.shippingAddress?.firstName || undefined,
		last_name: init.data.customer?.last_name || event.data.checkout.shippingAddress?.lastName || undefined,
		address1: event.data.checkout.shippingAddress?.address1 ?? undefined,
		city: event.data.checkout.shippingAddress?.city ?? undefined,
		country: event.data.checkout.shippingAddress?.country ?? undefined,
		country_code: event.data.checkout.shippingAddress?.countryCode ?? undefined,
		province: event.data.checkout.shippingAddress?.province ?? undefined,
		province_code: event.data.checkout.shippingAddress?.provinceCode ?? undefined,
		zip: event.data.checkout.shippingAddress?.zip ?? undefined,
    }, pageDetails(event.context)
    );
});

analytics.subscribe('checkout_completed', (event) => {
    if(GTM_LOADED == 0){
			loadGTM();
	}
	
    pushToDataLayer('gtm_purchase', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.checkout.currencyCode,
        transaction_id: event.data.checkout.order.id,
        value: event.data.checkout.totalPrice.amount,
        tax: event.data.checkout.totalTax.amount,
        net_value: parseFloat(event.data.checkout.totalPrice.amount)-parseFloat(event.data.checkout.totalTax.amount), 
        shipping: event.data.checkout.shippingLine.price.amount,
        sub_total: event.data.checkout.subtotalPrice.amount,
        discount_amount:event.data.checkout?.discountsAmount?.amount ?? undefined,
        checkout_token: event.data.checkout.token,
        coupon: event.data.checkout.discountApplications?.find(app => app.type === "DISCOUNT_CODE")?.title ?? undefined,
        payment_method: event.data.checkout?.transactions?.[0]?.paymentMethod?.type ?? undefined,
        items: event.data.checkout.lineItems.map(item => ({
            item_brand: item.variant.product.vendor,
            item_id: item.variant.sku === '' ? item.variant.id : item.variant.sku,
            item_category: item.variant?.product?.type ?? undefined,
            image: item.variant.image.src,
            variant_id: item.variant.id,
            variant: item.variant.title,
            product_id: item.variant.product.id,
            item_name: item.title,
            quantity: item.quantity,
            price: item.variant.price.amount,
            coupon: (item.discountAllocations && item.discountAllocations.length > 0) ? item.discountAllocations[0].discountApplication.title : undefined,
            discount: (item.discountAllocations && item.discountAllocations.length > 0) ? item.discountAllocations[0].amount.amount : undefined,
        }))
    },{
		id: event.data.checkout.order.customer?.id ?? undefined,
		client_id: event.clientId,
		email: init.data.customer?.email || event.data.checkout?.email || undefined,
		phone: init.data.customer?.phone || event.data.checkout?.phone || undefined,
		first_name: init.data.customer?.first_name || event.data.checkout.shippingAddress?.firstName || undefined,
		last_name: init.data.customer?.last_name || event.data.checkout.shippingAddress?.lastName || undefined,
		address1: event.data.checkout.shippingAddress?.address1 ?? undefined,
		city: event.data.checkout.shippingAddress?.city ?? undefined,
		country: event.data.checkout.shippingAddress?.country ?? undefined,
		country_code: event.data.checkout.shippingAddress?.countryCode ?? undefined,
		province: event.data.checkout.shippingAddress?.province ?? undefined,
		province_code: event.data.checkout.shippingAddress?.provinceCode ?? undefined,
		zip: event.data.checkout.shippingAddress?.zip ?? undefined,
		is_first_order: event.data.checkout.order.customer?.isFirstOrder ?? undefined,
    }, pageDetails(event.context)
    );
});

analytics.subscribe('payment_info_submitted', (event) => {
	
	if(GTM_LOADED == 0){
			loadGTM();
	}
	
    pushToDataLayer('gtm_add_payment_info', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.checkout.currencyCode,
        value: event.data.checkout.totalPrice.amount,
        tax: event.data.checkout.totalTax.amount,
        shipping: event.data.checkout.shippingLine.price.amount,
        sub_total: event.data.checkout.subtotalPrice.amount,
        checkout_token: event.data.checkout.token,
        coupon: event.data.checkout.discountApplications?.find(app => app.type === "DISCOUNT_CODE")?.title ?? undefined,
        items: event.data.checkout.lineItems.map(item => ({
            item_brand: item.variant.product.vendor,
            item_id: item.variant.sku === '' ? item.variant.id : item.variant.sku,
            item_category: item.variant?.product?.type ?? undefined,
            image: item.variant.image.src,
            variant_id: item.variant.id,
            variant: item.variant.title,
            product_id: item.variant.product.id,
            item_name: item.title,
            quantity: item.quantity,
            price: item.variant.price.amount,
            coupon: (item.discountApplications && item.discountApplications.length > 0) ? item.discountApplications[0].title : undefined,
            discount: (item.discountApplications && item.discountApplications.length > 0) ? item.discountApplications[0].amount.amount : undefined,
        }))
    },{
		id: init.data.customer?.id ?? undefined,
		client_id: event.clientId,
		email: init.data.customer?.email || event.data.checkout?.email || undefined,
		phone: init.data.customer?.phone || event.data.checkout?.phone || undefined,
		first_name: init.data.customer?.first_name || event.data.checkout.shippingAddress?.firstName || undefined,
		last_name: init.data.customer?.last_name || event.data.checkout.shippingAddress?.lastName || undefined,
		address1: event.data.checkout.shippingAddress?.address1 ?? undefined,
		city: event.data.checkout.shippingAddress?.city ?? undefined,
		country: event.data.checkout.shippingAddress?.country ?? undefined,
		country_code: event.data.checkout.shippingAddress?.countryCode ?? undefined,
		province: event.data.checkout.shippingAddress?.province ?? undefined,
		province_code: event.data.checkout.shippingAddress?.provinceCode ?? undefined,
		zip: event.data.checkout.shippingAddress?.zip ?? undefined,
    }, pageDetails(event.context)
    );
});

analytics.subscribe('checkout_contact_info_submitted', (event) => {

	if(GTM_LOADED == 0){
			loadGTM();
	}
	
    pushToDataLayer('gtm_contact_info', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.checkout.currencyCode,
        value: event.data.checkout.totalPrice.amount,
        tax: event.data.checkout.totalTax.amount,
        shipping: event.data.checkout.shippingLine.price.amount,
        sub_total: event.data.checkout.subtotalPrice.amount,
        checkout_token: event.data.checkout.token,
        coupon: event.data.checkout.discountApplications?.find(app => app.type === "DISCOUNT_CODE")?.title ?? undefined,
        items: event.data.checkout.lineItems.map(item => ({
            item_brand: item.variant.product.vendor,
            item_id: item.variant.sku === '' ? item.variant.id : item.variant.sku,
            item_category: item.variant?.product?.type ?? undefined,
            image: item.variant.image.src,
            variant_id: item.variant.id,
            variant: item.variant.title,
            product_id: item.variant.product.id,
            item_name: item.title,
            quantity: item.quantity,
            price: item.variant.price.amount,
            coupon: (item.discountApplications && item.discountApplications.length > 0) ? item.discountApplications[0].title : undefined,
            discount: (item.discountApplications && item.discountApplications.length > 0) ? item.discountApplications[0].amount.amount : undefined,
        }))
    },{
		id: init.data.customer?.id ?? undefined,
		client_id: event.clientId,
		email: init.data.customer?.email || event.data.checkout?.email || undefined,
		phone: init.data.customer?.phone || event.data.checkout?.phone || undefined,
		first_name: init.data.customer?.first_name || event.data.checkout.shippingAddress?.firstName || undefined,
		last_name: init.data.customer?.last_name || event.data.checkout.shippingAddress?.lastName || undefined,
		address1: event.data.checkout.shippingAddress?.address1 ?? undefined,
		city: event.data.checkout.shippingAddress?.city ?? undefined,
		country: event.data.checkout.shippingAddress?.country ?? undefined,
		country_code: event.data.checkout.shippingAddress?.countryCode ?? undefined,
		province: event.data.checkout.shippingAddress?.province ?? undefined,
		province_code: event.data.checkout.shippingAddress?.provinceCode ?? undefined,
		zip: event.data.checkout.shippingAddress?.zip ?? undefined,
    }, pageDetails(event.context)
    );
});

analytics.subscribe('checkout_address_info_submitted', (event) => {
	
	if(GTM_LOADED == 0){
			loadGTM();
	}
	
    pushToDataLayer('gtm_add_shipping_info', {
        event_id: event.id,
        event_category: 'ecommerce',
        event_timestamp: event.timestamp
    }, {
        currency: event.data.checkout.currencyCode,
        value: event.data.checkout.totalPrice.amount,
        tax: event.data.checkout.totalTax.amount,
        shipping: event.data.checkout.shippingLine.price.amount,
        sub_total: event.data.checkout.subtotalPrice.amount,
        checkout_token: event.data.checkout.token,
        coupon: event.data.checkout.discountApplications?.find(app => app.type === "DISCOUNT_CODE")?.title ?? undefined,
        items: event.data.checkout.lineItems.map(item => ({
            item_brand: item.variant.product.vendor,
            item_id: item.variant.sku === '' ? item.variant.id : item.variant.sku,
            item_category: item.variant?.product?.type ?? undefined,
            image: item.variant.image.src,
            variant_id: item.variant.id,
            variant: item.variant.title,
            product_id: item.variant.product.id,
            item_name: item.title,
            quantity: item.quantity,
            price: item.variant.price.amount,
            coupon: (item.discountApplications && item.discountApplications.length > 0) ? item.discountApplications[0].title : undefined,
            discount: (item.discountApplications && item.discountApplications.length > 0) ? item.discountApplications[0].amount.amount : undefined,
        }))
    }, {
		id: init.data.customer?.id ?? undefined,
		client_id: event.clientId,
		email: init.data.customer?.email || event.data.checkout?.email || undefined,
		phone: init.data.customer?.phone || event.data.checkout?.phone || undefined,
		first_name: init.data.customer?.first_name || event.data.checkout.shippingAddress?.firstName || undefined,
		last_name: init.data.customer?.last_name || event.data.checkout.shippingAddress?.lastName || undefined,
		address1: event.data.checkout.shippingAddress?.address1 ?? undefined,
		city: event.data.checkout.shippingAddress?.city ?? undefined,
		country: event.data.checkout.shippingAddress?.country ?? undefined,
		country_code: event.data.checkout.shippingAddress?.countryCode ?? undefined,
		province: event.data.checkout.shippingAddress?.province ?? undefined,
		province_code: event.data.checkout.shippingAddress?.provinceCode ?? undefined,
		zip: event.data.checkout.shippingAddress?.zip ?? undefined,
    }, pageDetails(event.context)
    );
});
