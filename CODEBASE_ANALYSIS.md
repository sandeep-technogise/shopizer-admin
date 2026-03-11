# Shopizer Admin - Codebase Analysis

## Project Overview

**Shopizer Admin** is an Angular 11 e-commerce administration web application for managing online stores. It's built with Angular CLI 11.2.x and tested with Node v12.22.7.

---

## Architecture

### Framework & Pattern

- Angular 11 with TypeScript 4.0.8
- Component-based architecture with lazy-loaded modules
- Feature modules organized by business domain
- Shared services using dependency injection
- HTTP interceptors for authentication and error handling

### Key Architectural Layers

1. **@core** - Core business logic, data interfaces, mock services, utilities
2. **@theme** - UI components, layouts, styles, pipes, directives
3. **pages** - Feature modules (catalogue, orders, customers, etc.)
4. **shared** - Cross-cutting concerns (guards, interceptors, services, models)

### Routing Strategy

- Hash-based routing (`useHash: true`)
- Lazy-loaded feature modules
- Route guards for authentication (`AuthGuard`)
- Main routes: `/auth`, `/pages`, `/gallery`

---

## Project Structure

```
src/app/
├── @core/              # Core module (data interfaces, mock services, utils)
├── @theme/             # Theme module (UI components, layouts, styles)
├── pages/              # Feature modules
│   ├── auth/           # Authentication (login, register, reset password)
│   ├── catalogue/      # Products, categories, brands, options, types
│   ├── content/        # Pages, boxes, images, files
│   ├── customers/      # Customer management
│   ├── home/           # Dashboard
│   ├── orders/         # Order management
│   ├── payment/        # Payment methods configuration
│   ├── shipping/       # Shipping configuration
│   ├── store-management/ # Store settings and multi-store
│   ├── tax-management/ # Tax classes and rates
│   ├── user-management/ # Admin users
│   └── shared/         # Shared services, guards, models, components
└── app.module.ts       # Root module
```

---

## Main Dependencies

### Core Framework

- `@angular/core` ^11.2.14
- `@angular/router` ^11.2.14
- `@angular/forms` ^11.2.14
- `rxjs` ^6.5.5

### UI Framework

- `@nebular/theme` ^6.2.0 - Main UI component library
- `@ng-bootstrap/ng-bootstrap` ^6.1.0
- `bootstrap` 4.3.1
- `eva-icons` ^1.1.3

### Rich Components

- `ng2-smart-table` ^1.5.0 - Data tables
- `@swimlane/ngx-charts` ^13.0.2 - Charts
- `chart.js` ^2.9.3
- `echarts` ^4.0.2
- `summernote` ^0.8.20 - WYSIWYG editor
- `ngx-toastr` ^12.1.0 - Notifications
- `ngx-lightbox` ^2.5.1 - Image viewer
- `angular-tree-component` ^8.5.6

### File Management

- `ng6-file-man` ^3.0.2
- `ngx-dropzone` ^2.2.2
- `ngx-awesome-uploader` ^10.0.4
- `fine-uploader` 5.16.2

### Internationalization

- `@ngx-translate/core` ^12.1.2
- `@ngx-translate/http-loader` ^5.0.0

### Date Handling

- `@nebular/date-fns` 5.0.0
- `@nebular/moment` ^6.2.0
- `date-fns` ^2.25.0

---

## Build Configuration

### Development

```bash
npm install --legacy-peer-deps
ng serve -o
# Runs on http://localhost:4200
```

### Production Build

```bash
ng build
# or
npm run build  # Uses --max_old_space_size=8192
```

### Docker

```bash
docker run \
  -e "APP_BASE_URL=http://localhost:9090/api" \
  -it --rm -p 4200:80 shopizerecomm/shopizer-admin
```

### Build Features

- AOT compilation enabled in production
- Source maps disabled in production
- Output hashing for cache busting
- Build optimizer enabled
- Vendor chunk splitting disabled

---

## API Endpoints

### Base URLs

- **Main API:** `http://localhost:8080/api`
- **Shipping API:** `http://localhost:9090/shipping/api/v1`

### Authentication & Users

- `POST /v1/private/login` - User login
- `GET /v1/auth/refresh` - Refresh token
- `POST /v1/user/password/reset/request` - Password reset request
- `GET /v1/private/users/{id}` - Get user by ID
- `GET /v1/private/user/profile` - Get current user profile
- `POST /v1/private/user/unique` - Check username uniqueness

### Store Management

- `GET /v1/store/{code}` - Get store by code
- `GET /v1/private/stores` - List stores
- `GET /v1/private/stores/names` - Get store names
- `GET /v1/store/languages` - Get supported languages

### Products & Catalogue

- `GET /v2/products` - List products
- `PATCH /v1/private/product/{id}` - Update product (partial)
- `PUT /v2/private/product/{id}` - Update product (full)
- `GET /v1/private/product/{id}/inventory` - Get product inventory
- `POST /v1/private/product/inventory` - Create inventory
- `GET /v1/private/product/{productId}/attributes` - Get product attributes
- `POST /v1/private/product/{productId}/attribute` - Add attribute
- `PUT /v1/private/product/{productId}/attribute/{attributeId}` - Update attribute
- `GET /v1/product/{productId}/images` - Get product images
- `POST /v1/private/product/{id}/images` - Upload product images
- `DELETE /v1/private/product/{productId}/image/{imageId}` - Delete image

### Categories

- `GET /v1/category` - List categories
- `GET /v1/category/{id}` - Get category by ID
- `GET /v1/category/product/{id}` - Get category products

### Product Options

- `GET /v1/private/product/options` - List options
- `POST /v1/private/product/option` - Create option
- `PUT /v1/private/product/option/{id}` - Update option
- `GET /v1/private/product/options/values` - List option values
- `DELETE /v1/private/product/option/value/{id}` - Delete option value
- `GET /v1/private/product/option/value/{id}` - Get option value
- `POST /v1/private/product/option/value/{optionValueId}/image` - Upload option value image
- `DELETE /v1/private/product/option/value/{optionValueId}/image` - Delete option value image

### Variations

- `GET /v2/private/product/variation/unique` - Check variation uniqueness
- `POST /v2/private/product/variation` - Create variation
- `GET /v2/private/product/variation` - List variations

### Brands (Manufacturers)

- `GET /v1/private/manufacturers/` - List brands
- `PUT /v1/private/manufacturer/{id}` - Update brand
- `GET /v1/manufacturers/{id}` - Get brand by ID

### Product Types

- `GET /v1/private/products/types` - List product types
- `GET /v1/private/products/type/{id}` - Get product type
- `POST /v1/private/products/type` - Create product type

### Product Groups

- `GET /v1/private/product/groups` - List product groups
- `POST /v1/private/product/group` - Create product group

### Catalogues

- `GET /v1/private/catalogs` - List catalogs
- `GET /v1/private/catalog/{id}` - Get catalog
- `POST /v1/private/catalog` - Create catalog

### Orders

- `GET /v1/private/orders` - List orders
- `GET /v1/private/orders/{orderID}` - Get order details

### Customers

- `GET /v1/private/customers` - List customers
- `GET /v1/private/customer/{customerID}` - Get customer
- `DELETE /v1/private/customer/{customerID}` - Delete customer

### Content Management

- `GET /v1/private/content/pages` - List pages
- `GET /v1/content/pages/{code}` - Get page by code
- `POST /v1/private/content/page` - Create page
- `PUT /v1/private/content/page/{id}` - Update page
- `DELETE /v1/private/content/{id}` - Delete content
- `GET /v1/private/content/page/{code}/exists` - Check page exists
- `GET /v1/private/content/boxes/` - List boxes
- `GET /v1/private/content/boxes/{code}` - Get box
- `POST /v1/private/content/box` - Create box
- `PUT /v1/private/content/box/{id}` - Update box
- `GET /v1/private/content/box/{code}/exists` - Check box exists
- `GET /v1/content/folder` - List content folders
- `POST /v1/private/file` - Upload file
- `DELETE /v1/private/content/` - Delete content by name

### Shipping

- `GET /v1/private/shipping/expedition` - Get shipping expedition config
- `POST /v1/private/shipping/expedition` - Update expedition config
- `GET /v1/private/shipping/origin` - Get shipping origin
- `POST /v1/private/shipping/origin` - Update shipping origin

### Payment

- `GET /v1/private/modules/payment` - List payment modules
- `GET /v1/private/modules/payment/{type}` - Get payment module
- `POST /v1/private/modules/payment/` - Configure payment module

### Tax Management

- `GET /v1/private/tax/class` - List tax classes
- `DELETE /v1/private/tax/class/{id}` - Delete tax class
- `GET /v1/private/tax/class/unique` - Check tax class uniqueness

### Configuration & Reference Data

- `GET /v1/country` - List countries
- `GET /v1/zones` - Get zones/provinces by country
- `GET /v1/currency` - List currencies
- `GET /v1/measures` - Get weight and size measures
- `GET /v1/config` - Get site configuration
- `GET /v1/languages` - List system languages
- `GET /v1/sec/private/groups` - List user groups
- `GET /actuator/health/ping` - Health check

---

## Key Features

### Multi-tenancy

- Supports multiple stores (MARKETPLACE, BTB, STANDARD modes)
- Store-specific configurations and data

### Role-Based Access

- **Roles:** Superadmin, Admin, Admin Retail, Admin Catalogue, Admin Store, Admin Order, Admin Content, Customer
- Menu items and routes protected by role guards

### Internationalization

- Multi-language support (English, French by default)
- Translation files in `/assets/i18n/`
- Language-specific content management

### Authentication

- JWT-based authentication
- Token refresh mechanism
- HTTP interceptors for auth headers
- Password reset flow

### File Management

- Image upload and management
- File browser component
- Support for product images, option value images
- Content images and files

### Rich Editing

- WYSIWYG editor (Summernote) for content pages
- Image lightbox for viewing
- Drag-and-drop file uploads

---

## Security Features

- `AuthGuard` for route protection
- `AuthInterceptor` for adding JWT tokens
- `GlobalHttpInterceptorService` for error handling
- Role-based menu visibility
- Secure password reset flow

---

## Default Credentials

- **Username:** admin@shopizer.com
- **Password:** password

---

## Environment Configuration

Configuration is managed in `src/environments/`:

```typescript
export const environment = {
    production: false,
    googleApiKey: '',
    mode: 'STANDARD', // MARKETPLACE | BTB | STANDARD
    apiUrl: "http://localhost:8080/api",
    shippingApi: 'http://localhost:9090/shipping/api/v1',
    client: {
        language: {
            default: 'en',
            array: ['fr', 'en']
        }
    }
};
```

---

## Testing

```bash
# Unit tests
npm run test

# E2E tests
npm run e2e

# Test coverage
npm run test:coverage
```

---

## Additional Scripts

```bash
# Linting
npm run lint
npm run lint:fix
npm run lint:styles

# Documentation
npm run docs
npm run docs:serve

# Changelog
npm run release:changelog
```

---

## Docker Support

### Dockerfile

Located at project root, builds production-ready nginx container.

### Docker Compose

Configuration available in `/docker` directory.

---

## Notes

- Uses `--legacy-peer-deps` for npm install due to dependency conflicts
- Requires backend API running on `http://localhost:8080/api`
- Shipping service on `http://localhost:9090/shipping/api/v1`
- Application mode (MARKETPLACE/BTB/STANDARD) affects category and options management scope
