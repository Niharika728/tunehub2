<!DOCTYPE html>
<html xmlns:th="https://www.thymeleaf.org">
<head>
<meta charset="ISO-8859-1">
	
	<title>Payment</title>
	<script src="https://checkout.razorpay.com/v1/checkout.js"></script>
	<script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>

</head>
<body>
<div>

		<h1>Why premium ? </h1>
		<p>text...................</p>
		
    	<form id="payment-form">
	        <button type="submit" class="buy-button" >BUY</button>
	    </form>
   
</div>

<script>
$(document).ready(function() 
{
    $(".buy-button").click(function(e) 
    {
        e.preventDefault();
        var form = $(this).closest('form');
        
        
        createOrder();
    });
});

function createOrder() 
{
	
    $.post("/createOrder")
        .done(function(order) 
        {
            order = JSON.parse(order);
            var options = 
            {
                "key": "rzp_test_nubkjMCCQtr5iq",
                "amount": order.amount_due.toString(),
                "currency": "INR",
                "name": "Tune Hub",
                "description": "Test Transaction",
                "order_id": order.id,
                "handler": function (response) 
                {
                    verifyPayment(response.razorpay_order_id, response.razorpay_payment_id, response.razorpay_signature);
                },
                "prefill": 
                {
                    "name": "Your Name",
                    "email": "test@example.com",
                    "contact": "9999999999"
                },
                "notes": 
                {
                    "address": "Your Address"
                },
                "theme": 
                {
                    "color": "#F37254"
                }
            };
            var rzp1 = new Razorpay(options);
            rzp1.open();
        })
        .fail(function(error) 
        {
            console.error("Error:", error);
        });
}

function verifyPayment(orderId, paymentId, signature) 
{
     $.post("/verify", { orderId: orderId, paymentId: paymentId, signature: signature })
         .done(function(isValid) 
         {
             if (isValid) 
             {	
             	alert("Payment successful");
                window.location.href='Payment-success';
             } 
             else 
             {
                 alert("Payment failed");
                 window.location.href='payment-failure';
             }
         })
         .fail(function(error) 
         {
             console.error("Error:", error);
         });
}
</script>
</body>
</html>
