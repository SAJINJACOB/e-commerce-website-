# e-commerce-website-
# Import necessary modules and libraries
import os
from django.shortcuts import render
from django.http import HttpResponse
from django.views.decorators.csrf import csrf_exempt
from django.db import models
from django.utils import timezone
from django.core.exceptions import ValidationError
from django.core.mail import send_mail

# Set up the Django project
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'ecommerce.settings')

# Create the Django app
app = Django(__name__)

# Define the Product model with necessary fields
class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    description = models.TextField()
    image = models.ImageField(upload_to='products/')
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    # Define custom validation for the price field
    def clean_price(self):
        if self.price <= 0:
            raise ValidationError('Price must be greater than 0')
        return self.price
    
    # Define the __str__ method for the model
    def __str__(self):
        return self.name

# Define the view for the homepage
def index(request):
    # Query the database for all products
    products = Product.objects.all()
    # Render the index.html template with the products as context
    return render(request, 'index.html', {'products': products})

# Define the view for the product detail page
def product_detail(request, product_id):
    # Query the database for the product with the given id
    product = Product.objects.get(id=product_id)
    # Render the product_detail.html template with the product as context
    return render(request, 'product_detail.html', {'product': product})

# Define the view for the checkout page
def checkout(request):
    # Get the product id from the request
    product_id = request.GET.get('product_id')
    # Query the database for the product with the given id
    product = Product.objects.get(id=product_id)
    # Render the checkout.html template with the product as context
    return render(request, 'checkout.html', {'product': product})

# Define the view for the checkout confirmation page
@csrf_exempt
def checkout_confirmation(request):
    # Get the product id and customer information from the request
    product_id = request.POST.get('product_id')
    customer_name = request.POST.get('name')
    customer_email = request.POST.get('email')
    customer_address = request.POST.get('address')
    # Query the database for the product with the given id
    product = Product.objects.get(id=product_id)
    # Create a new Order instance with the customer information and product
    order = Order
