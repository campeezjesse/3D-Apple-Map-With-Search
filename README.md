# 3D-Apple-Map-With-Search
Searchable fly over map
//
//  ViewController.swift
//  CapmingApp
//
//  Created by user1 on 1/25/18.
//  Copyright © 2018 campeez. All rights reserved.
//

import UIKit
import SceneKit
import ARKit
import MapKit
import CoreMotion

class ViewController: UIViewController, ARSCNViewDelegate, UISearchBarDelegate {
    
    let motionManager = CMMotionManager()
    
    @IBOutlet var mapView: MKMapView!
    
    @IBAction func searchButton(_ sender: Any) {
        let searchController = UISearchController (searchResultsController: nil)
        searchController.searchBar.delegate = self
        present(searchController, animated: true, completion: nil)
    }
    
    
    //Ignoring User
    func searchBarSearchButtonClicked(_ searchBar: UISearchBar) {
        UIApplication.shared.beginIgnoringInteractionEvents()
        
        //Activity Indicator
        let activityIndicator = UIActivityIndicatorView()
        activityIndicator.activityIndicatorViewStyle = UIActivityIndicatorViewStyle.gray
        activityIndicator.center = self.view.center
        activityIndicator.hidesWhenStopped = true
        activityIndicator.startAnimating()
        
        self.view.addSubview(activityIndicator)
        
        //Hide Search and Keyboard
        searchBar.resignFirstResponder()
        dismiss(animated: true, completion: nil)
        
        //Create the search request
        let searchRequest = MKLocalSearchRequest ()
        searchRequest.naturalLanguageQuery = searchBar.text
        let activeSearch = MKLocalSearch(request: searchRequest)
        activeSearch.start { (responce, error) in
            activityIndicator.stopAnimating()
            
            UIApplication.shared.endIgnoringInteractionEvents()
            
            if responce == nil
            {
                print("ERROR")
            }
            else
            {
                //Remove annotations
                let annotations = self.mapView.annotations
                self.mapView.removeAnnotations(annotations)
                
                //Get Data
                let latitude = responce?.boundingRegion.center.latitude
                let longitude = responce?.boundingRegion.center.longitude
                
                // Create Annotation / Change to AR?
                let annotation = MKPointAnnotation ()
                annotation.title = searchBar.text
                annotation.coordinate = CLLocationCoordinate2DMake(latitude!, longitude!)
                self.mapView.addAnnotation(annotation)
                
                
                //Zooming In
                let coordinate: CLLocationCoordinate2D = CLLocationCoordinate2DMake(latitude!, longitude!)
                let span = MKCoordinateSpanMake(0.1, 0.1)
                let region = MKCoordinateRegionMake(coordinate, span)
                self.mapView.setRegion(region, animated: true)
                
                // 3D Camera
                let mapCamera = MKMapCamera()
                mapCamera.centerCoordinate = coordinate
                mapCamera.pitch = 65
                mapCamera.altitude = 1000
                mapCamera.heading = 90
                
                // Set MKmapView camera property
                self.mapView.camera = mapCamera
                
                
                
            }
            
        }
    }
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        
        
        
        
    }
    
    
    
    
    
    
    
}

