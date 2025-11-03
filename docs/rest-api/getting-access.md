# Getting Access to Mapy.com REST API

Getting started with the Mapy.com REST API is simple and free. You can begin using the API immediately after creating an account.

## Quick Start - Get Your Free API Key

1. **Log in with Seznam account**: Visit [My Account portal](https://developer.mapy.com/account/) and use your Seznam account email
2. **Create an API Project**: Create a new project for your application
3. **Get your API key**: The first API key is automatically created within the project
4. **Start using the API**: You can begin making API calls immediately

**Important**: Use a real email address that you monitor regularly, as important notifications about your API usage are sent to this email.

For detailed step-by-step instructions, see the [official getting started guide](https://developer.mapy.com/rest-api-mapy-cz/how-to-start/).

## API Key Management

You can create and manage API keys in the [My Account portal](https://developer.mapy.com/account/):

- Create separate keys for development and production environments
- Generate different keys for web and mobile versions of your app
- Monitor usage and credit consumption
- Secure your production API keys properly

## Authentication

Include your API key in every request using the `apikey` parameter:

```
https://api.mapy.com/v1/[endpoint]?apikey=YOUR_API_KEY&[other_parameters]
```

## First API Call

Test your API key with a simple geocoding request:

```bash
curl "https://api.mapy.com/v1/geocode?apikey=YOUR_API_KEY&query=Prague"
```

## Pricing Overview

The Mapy.com REST API uses a credit-based system:

- **Free credits included**: Both Basic and Extended tariffs include free credits
- **No charged consumption without consent**: You only pay for what you actually use
- **Extended tariff available**: Up to 10 million free credits monthly for publicly accessible projects

For detailed pricing information, visit the [official pricing page](https://developer.mapy.com/pricing/).

## Testing and Development

- **Interactive testing**: Use the [Swagger UI](https://api.mapy.com/v1/docs/) to test API calls
- **Tutorials**: Find practical examples in the [REST API tutorials](https://developer.mapy.com/rest-api-mapy-cz/)
- **Testing labs**: Try individual function calls and see responses

## Security Best Practices

- **Never commit API keys to version control**: Use environment variables or secure configuration
- **Use server-side proxies for sensitive calls**: Don't expose your API key in client-side code for production applications
- **Monitor your usage**: Track credit consumption in the [My Account portal](https://developer.mapy.com/account/)
- **Rotate keys if compromised**: Generate new keys immediately if you suspect unauthorized access

## Related

- [REST API Documentation](README.md)
- [Map Tiles](map-tiles.md)
- [Static Maps](static-maps.md)
- [Forward Geocoding](forward-geocoding.md)
- [Reverse Geocoding](reverse-geocoding.md)
- [Routing](routing.md)
- [Matrix Routing](matrix-routing.md)
- [Elevation](elevation.md)
- [Static Panorama](static-panorama.md)
- [Time Zones](time-zones.md)
