---
layout: wiki
title: Taxes
---
## Taxes

- [Overview](#overview)
- [Where are taxes applied?](#where-are-taxes-applied)
- [Example Tax Implementation](#example-tax-implementation)
 - [Example Tax Service Locator](#example-tax-service-locator)
 - [Example Tax Service](#example-tax-service)

## Overview

Taxes are considered implementation specific.  

The system interacts with tax calculation with the help of two interfaces:

The tax service locator implements the interface `\LizardsAndPumpkins\Import\Tax\TaxServiceLocator`.
The `get()` method returns an instance of `\LizardsAndPumpkins\Import\Tax\TaxService`.

The tax service locator is responsible for finding the correct tax service based on the presented options. The `\LizardsAndPumpkins\Import\Tax\TaxServiceLocator::get()` methods signature is kept quite generic because different implementations will need to pass different options, based on business requirements.

The `\LizardsAndPumpkins\Import\Tax\TaxService::applyTo()` method is responsible for returning a new `Price` instance with the appropriate tax rates applied.

## Where are taxes applied?

Currently taxes are applied during price snippet rendering.

The following method is an example how `\LizardsAndPumpkins\Import\Price\PriceSnippetRenderer` applies taxes to a product price.

```php
    /**
     * @param Product $product
     * @param Context $context
     * @return Price
     */
    private function getPriceIncludingTax(Product $product, Context $context)
    {
        $amount = $product->getFirstValueOfAttribute($this->priceAttributeCode);
        $taxServiceLocatorOptions = [
            TaxServiceLocator::OPTION_WEBSITE => $context->getValue(ContextWebsite::CODE),
            TaxServiceLocator::OPTION_PRODUCT_TAX_CLASS => $product->getTaxClass(),
            TaxServiceLocator::OPTION_COUNTRY => $context->getValue(ContextCountry::CODE),
        ];
        
        return $this->taxServiceLocator->get($taxServiceLocatorOptions)->applyTo(new Price($amount));
    }
```

----
## Example Tax Implementation

### Example Tax Service Locator

```php
<?php

namespace LizardsAndPumpkins\Product\Tax;

use LizardsAndPumpkins\Context\Context;
use LizardsAndPumpkins\Country\Country;
use LizardsAndPumpkins\Import\Tax\Exception\UnableToLocateTaxServiceException;
use LizardsAndPumpkins\Import\Tax\TaxRates\ExampleTaxRate;
use LizardsAndPumpkins\Website\Website;

class ExampleTaxServiceLocator implements TaxServiceLocator
{
    private static $rateTable = [
        // [websites],       [tax rates],       country, rate
        [['shop1', 'shop2'], ['standard-vat'],     'DE', 19],
        [['shop1', 'shop2'], ['standard-vat'],     'AT', 20],
        [['shop1', 'shop2'], ['reduced-vat'],      'DE',  7],
        [['shop1', 'shop2'], ['reduced-vat'],      'AT', 20],
        [['shop3'],          ['special-products'], 'DE', 25],
        [['shop3'],          ['standard-vat'],     'DE', 19],
    ];
    
    private static $websiteIdx = 0;
    private static $taxClassIdx = 1;
    private static $countryIdx = 2;
    private static $rateIdx = 3;

    /**
     * @var Website
     */
    private $website;

    /**
     * @var Country
     */
    private $country;

    /**
     * @var ProductTaxClass
     */
    private $taxClass;

    /**
     * @param mixed[] $options
     * @return TaxService
     */
    public function get(array $options)
    {
        $this->website = $this->getWebsiteFromOptions($options);
        $this->taxClass = $this->getProductTaxClassFromOptions($options);
        $this->country = $this->getCountryFromOptions($options);
        
        return $this->findRule();
    }

    /**
     * @return TwentyOneRunTaxRate
     */
    private function findRule()
    {
        foreach (self::$rateTable as $rule) {
            if ($this->isMatchingRule($rule)) {
                return ExampleTaxRate::fromInt($rule[self::$rateIdx]);
            }
        }
        throw $this->createUnableToLocateServiceException();
    }

    /**
     * @param mixed[] $rule
     * @return bool
     */
    private function isMatchingRule(array $rule)
    {
        return
            in_array((string) $this->website, $rule[self::$websiteIdx]) &&
            in_array((string) $this->taxClass, $rule[self::$taxClassIdx]) &&
            (string) $this->country === $rule[self::$countryIdx];
    }

    /**
     * @param mixed[] $options
     * @return Country
     */
    private function getCountryFromOptions(array $options)
    {
        return Country::from2CharIso3166($options[self::OPTION_COUNTRY]);
    }

    /**
     * @param mixed[] $options
     * @return ProductTaxClass
     */
    private function getProductTaxClassFromOptions(array $options)
    {
        return ProductTaxClass::fromString($options[self::OPTION_PRODUCT_TAX_CLASS]);
    }

    /**
     * @param mixed[] $options
     * @return Website
     */
    private function getWebsiteFromOptions(array $options)
    {
        return Website::fromString($options[self::OPTION_WEBSITE]);
    }

    /**
     * @return UnableToLocateTaxServiceException
     */
    private function createUnableToLocateServiceException()
    {
        $message = sprintf(
            'Unable to locate a tax service for website "%s", product tax class "%s" and country "%s"',
            $this->website,
            $this->taxClass,
            $this->country
        );
        return new UnableToLocateTaxServiceException($message);
    }
}

```

### Example Tax Service

```php
<?php

namespace LizardsAndPumpkins\Product\Tax\TaxRates;

use LizardsAndPumpkins\Product\Price;
use LizardsAndPumpkins\Product\Tax\TaxRates\Exception\InvalidTaxRateException;
use LizardsAndPumpkins\Product\Tax\TaxService;

class ExampleTaxRate implements TaxService
{
    /**
     * @var int
     */
    private $rate;

    /**
     * @param int $rate
     */
    private function __construct($rate)
    {
        $this->validateRate($rate);
        $this->rate = $rate;
    }

    /**
     * @param int $rate
     * @return TwentyOneRunTaxRate
     */
    public static function fromInt($rate)
    {
        return new self($rate);
    }

    /**
     * @param int $rate
     */
    private function validateRate($rate)
    {
        if (!is_int($rate)) {
            $message = sprintf('The tax rate has to be an integer value, got "%s"', gettype($rate));
            throw new InvalidTaxRateException($message);
        }
        if (0 === $rate) {
            throw new InvalidTaxRateException('The tax rate must not be zero');
        }
    }

    /**
     * @return int
     */
    public function getRate()
    {
        return (int) ($this->getFactor() * 100 - 100);
    }

    /**
     * @param Price $price
     * @return Price
     */
    public function applyTo(Price $price)
    {
        $result = round($price->getAmount() * $this->getFactor(), 0, PHP_ROUND_HALF_DOWN);
        return new Price((int) $result);
    }

    /**
     * @return float
     */
    private function getFactor()
    {
        return 1 + $this->rate / 100;
    }
}
```
