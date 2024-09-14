import React, { useState, useEffect } from 'react';
import { Card, CardContent, CardFooter, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { ShoppingCart, Clock, CreditCard, History, Phone, Search } from 'lucide-react';
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert";

// ... (previous menuItems and deliveryTimeSlots remain unchanged)

const AULiveryApp = () => {
  const [cart, setCart] = useState([]);
  const [activeTab, setActiveTab] = useState('menu');
  const [selectedTimeSlot, setSelectedTimeSlot] = useState('');
  const [searchTerm, setSearchTerm] = useState('');
  const [filteredItems, setFilteredItems] = useState(menuItems);
  const [orderHistory, setOrderHistory] = useState([]);
  const [orderPlaced, setOrderPlaced] = useState(false);

  useEffect(() => {
    setFilteredItems(
      menuItems.filter(item => 
        item.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
        item.category.toLowerCase().includes(searchTerm.toLowerCase())
      )
    );
  }, [searchTerm]);

  // ... (previous addToCart, removeFromCart, and calculateTotal functions remain unchanged)

  const placeOrder = () => {
    if (cart.length === 0 || !selectedTimeSlot) {
      alert("Please add items to your cart and select a delivery time.");
      return;
    }
    const newOrder = {
      id: Date.now(),
      items: [...cart],
      total: calculateTotal(),
      deliveryTime: selectedTimeSlot,
      date: new Date().toLocaleString()
    };
    setOrderHistory([newOrder, ...orderHistory]);
    setCart([]);
    setSelectedTimeSlot('');
    setOrderPlaced(true);
    setTimeout(() => setOrderPlaced(false), 3000);
    setActiveTab('history');
  };

  const renderMenuItem = (item) => (
    <Card key={item.id} className="mb-4 bg-white text-blue-900">
      <CardHeader>
        <CardTitle>{item.name}</CardTitle>
        <div className="text-sm text-gray-500">{item.category}</div>
      </CardHeader>
      <CardContent>
        {Object.entries(item.prices).map(([size, price]) => (
          <div key={size} className="flex justify-between items-center mb-2">
            <span>{size.toUpperCase()}</span>
            <span>${price.toFixed(2)} ({item.calories[size]} cal)</span>
            <Button onClick={() => addToCart(item, size)} className="bg-blue-900 text-white hover:bg-blue-700">Add</Button>
          </div>
        ))}
      </CardContent>
    </Card>
  );

  // ... (previous renderCart and renderCheckout functions remain unchanged)

  const renderOrderHistory = () => (
    <Card className="bg-white text-blue-900">
      <CardHeader>
        <CardTitle>Order History</CardTitle>
      </CardHeader>
      <CardContent>
        {orderHistory.length === 0 ? (
          <p>You haven't placed any orders yet.</p>
        ) : (
          orderHistory.map(order => (
            <div key={order.id} className="mb-4 p-4 border rounded">
              <p className="font-bold">Order #{order.id}</p>
              <p>Date: {order.date}</p>
              <p>Delivery Time: {order.deliveryTime}</p>
              <p>Total: ${order.total}</p>
              <details>
                <summary>Items</summary>
                <ul>
                  {order.items.map((item, index) => (
                    <li key={index}>{item.name} ({item.size}) - ${item.prices[item.size].toFixed(2)}</li>
                  ))}
                </ul>
              </details>
            </div>
          ))
        )}
      </CardContent>
    </Card>
  );

  // ... (previous renderContact function remains unchanged)

  return (
    <div className="container mx-auto p-4 max-w-md" style={{ backgroundColor: '#FFA500' }}>
      <header className="text-center mb-6">
        <img src="/api/placeholder/100/100" alt="AU-Livery Logo" className="mx-auto mb-2" />
        <h1 className="text-3xl font-bold mb-2 text-blue-900">AU-Livery</h1>
        <p className="text-sm text-blue-900">Food Delivery for Averett University</p>
      </header>

      {orderPlaced && (
        <Alert className="mb-4">
          <AlertTitle>Order Placed Successfully!</AlertTitle>
          <AlertDescription>Your order has been placed and will be delivered at the selected time.</AlertDescription>
        </Alert>
      )}

      <Tabs value={activeTab} onValueChange={setActiveTab}>
        <TabsList className="grid w-full grid-cols-5 bg-blue-900">
          <TabsTrigger value="menu" className="text-white"><ShoppingCart className="h-4 w-4" /></TabsTrigger>
          <TabsTrigger value="cart" className="text-white"><ShoppingCart className="h-4 w-4" /></TabsTrigger>
          <TabsTrigger value="checkout" className="text-white"><CreditCard className="h-4 w-4" /></TabsTrigger>
          <TabsTrigger value="history" className="text-white"><History className="h-4 w-4" /></TabsTrigger>
          <TabsTrigger value="contact" className="text-white"><Phone className="h-4 w-4" /></TabsTrigger>
        </TabsList>
        <TabsContent value="menu">
          <div className="mb-4">
            <Input
              type="text"
              placeholder="Search menu..."
              value={searchTerm}
              onChange={(e) => setSearchTerm(e.target.value)}
              className="w-full"
            />
          </div>
          {filteredItems.map(renderMenuItem)}
        </TabsContent>
        <TabsContent value="cart">
          {renderCart()}
        </TabsContent>
        <TabsContent value="checkout">
          {renderCheckout()}
        </TabsContent>
        <TabsContent value="history">
          {renderOrderHistory()}
        </TabsContent>
        <TabsContent value="contact">
          {renderContact()}
        </TabsContent>
      </Tabs>
    </div>
  );
};

export default AULiveryApp;
