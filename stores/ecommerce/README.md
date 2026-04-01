# OpenFGA for Ecommerce Platforms

## Use Case

This model represents the authorization needs of an **ecommerce platform**, like Shopify, BigCommerce, WooCommerce, Magento, or Etsy. These platforms manage stores, product catalogs, customers, orders, fulfillment, and refunds.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own stores, products, customers, and orders.
- **Store roles**: Organization-level roles (`admin`, `store_manager`, `member`) control platform-wide access. Store-level roles (`owner`, `manager`, `staff`) control per-store operations. Managers inherit from the organization's `store_manager` role.
- **Parent-scoped product creation**: Product creation is checked on the store (`can_create_product`) rather than on product objects that do not exist yet.
- **Product management**: Store managers can create, edit, and delete products. Staff members who created a product can edit it. All store staff can view products and manage inventory.
- **Customer accounts**: Customers can view and edit their own account information. Store staff can view customer records, but only managers can edit them and only admins can delete them.
- **Order lifecycle**: Customers who placed an order can view and cancel it. Store staff can view, edit, and fulfill orders. Only managers can issue refunds. Only admins can delete orders.
- **Fulfillment access**: Store staff can mark orders as fulfilled/shipped, enabling warehouse workers to process shipments without manager-level access.
- **Refund authorization**: Only store managers and organization admins can process refunds, ensuring proper financial oversight.
- **Store ownership**: Store owners can manage store settings and delete the store. Staff can view but not modify store configuration.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `ecommerce` directory, run `fga model test --tests store.fga.yaml`
