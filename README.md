# science-fair-2018-plant

Python 3.5.2 (v3.5.2:4def2a2901a5, Jun 26 2016, 10:47:25) 
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "copyright", "credits" or "license()" for more information.
>>> WARNING: The version of Tcl/Tk (8.5.9) in use may be unstable.
Visit http://www.python.org/download/mac/tcltk/ for current information.
def makeYqlQuery(req):
    result = req.get('result')
    parameters = result.get('parameters')
    city = parameters.get('geo-city')
    if city is None:
        return None
    #print(city)
    return "select * from weather.forecast where woeid in (select woeid from geo.places(1) where text='" \
        + city + "')"


def makeWebhookResult(data, parameters):
    query = data.get('query')
    if query is None:
        return {}

    result = query.get('results')
    #print(json.dumps(parameters, indent=4))
    if result is None:
        return {'speech': 'No Result', 'displayText': 'No Result',
                'source': 'apiai-weather-webhook-sample'}
    channel = result.get('channel')
    if channel is None:
        return {'speech': 'No Channel', 'displayText': 'No Channel',
                'source': 'apiai-weather-webhook-sample'}

    item = channel.get('item')
    location = channel.get('location')
    units = channel.get('units')
    if location is None or item is None or units is None:
        return {'speech': 'No location or item or units',
                'displayText': 'No location or item or units',
                'source': 'apiai-weather-webhook-sample'}
    
    condition = item.get('condition')
    if condition is not None:
        plant = parameters.get('plant-type')
        moist = parameters.get('number')
        city = parameters.get('geo-city')
        temp = float(condition.get('temp'))
        fcast = parameters.get('Forcast')
        decision = ' need '
        if temp > 70 and moist > 25 :
             decision = ' does not need '
        elif temp < 70 and moist < 20 :
              decision = ' needs '
        result = {}
        if plant in ['cotton', 'tulips', 'wheat']:
            result['speech'] = "Yuvanshu. The temperature is {0} degrees Fahrenheit and the weather forecast is {1} in  {2} and the soil moisture is {3} percent. Based on your data, your  {4}  {5} water  ".format( temp, fcast, city,  moist,  plant, decision )
            result['displayText'] = result['speech']
            result['source'] = 'apiai-weather-webhook-sample'
        return result


if __name__ == '__main__':
    port = int(os.getenv('PORT', 5000))

    print('Starting app on port %d' % port)

    app.run(debug=False, port=port, host='0.0.0.0')
